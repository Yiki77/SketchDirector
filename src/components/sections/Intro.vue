<script lang="ts" setup>

import {

  nextTick,

  onBeforeUnmount,

  onMounted,

  reactive

} from 'vue'

import {

  Delete,

  Refresh,

  RefreshLeft,

  RefreshRight,

  ZoomIn,

  ZoomOut

} from '@element-plus/icons-vue'

// ============================================================

// Types

// ============================================================

interface GalleryConfig {

  id: string

  title: string

  caption: string

  svg: string

  draft: string

}

interface EditableObject {

  key: string

  label: string

  x: number

  y: number

  scale: number

  rotation: number

  centerX: number

  centerY: number

  deleted: boolean

}

interface GalleryItem extends GalleryConfig {

  loading: boolean

  error: string

  svgMarkup: string

  objects: EditableObject[]

  selectedKey: string | null

  draftExpanded: boolean

}

interface SvgBounds {

  minX: number

  minY: number

  maxX: number

  maxY: number

}

// ============================================================

// Three examples

// ============================================================

const BASE_URL =

  import.meta.env.BASE_URL || '/'

const withBase = (

  path: string

) => {

  if (

    path.startsWith('http://')

    || path.startsWith('https://')

    || path.startsWith('data:')

  ) {

    return path

  }

  const normalizedBase =

    BASE_URL.endsWith('/')

      ? BASE_URL

      : `${BASE_URL}/`

  return (

    normalizedBase

    + path.replace(/^\/+/, '')

  )

}

const moreResultsUrl =

  withBase('more-results.html')

const galleryConfigs: GalleryConfig[] = [

  {

    id: 'gallery-1',

    title: 'Example 1',

    caption: 'A chef in the kitchen.',

    svg: withBase('carousel/1-chef.svg'),

    draft: ''

  },

  {

    id: 'gallery-2',

    title: 'Example 2',

    caption:

      'A staggered city skyline of spires, domes, and clock towers; street trees and lampposts line the foreground, and flocks of birds fly in the sky.',

    svg: withBase('carousel/2-city.svg'),

    draft: ''

  },

  {

    id: 'gallery-3',

    title: 'Example 3',

    caption:

      'Four ducks are swimming in the pool.',

    svg: withBase('carousel/3-ducks.svg'),

    draft: ''

  }

]

const galleryItems =

  reactive<GalleryItem[]>(

    galleryConfigs.map(

      (config) => ({

        ...config,

        loading: true,

        error: '',

        svgMarkup: '',

        objects: [],

        selectedKey: null,

        draftExpanded: false

      })

    )

  )

// ============================================================

// Constants

// ============================================================

const SVG_NAMESPACE =

  'http://www.w3.org/2000/svg'

const MIN_SCALE = 0.25

const MAX_SCALE = 2.5

const SVG_FIT_PADDING_RATIO = 0.08

const SVG_FIT_MIN_PADDING = 16

const DEFAULT_SVG_WIDTH = 780

const DEFAULT_SVG_HEIGHT = 780

let stopActiveInteraction:

  (() => void)

  | null = null

// ============================================================

// General helpers

// ============================================================

const clamp = (

  value: number,

  minimum: number,

  maximum: number

) => {

  return Math.min(

    maximum,

    Math.max(

      minimum,

      value

    )

  )

}

const waitForNextFrame = () => {

  return new Promise<void>((resolve) => {

    window.requestAnimationFrame(() => {

      resolve()

    })

  })

}

const prettifyObjectName = (

  value: string,

  objectIndex: number

) => {

  if (

    !value

    || /^svg_\d+$/i.test(value)

  ) {

    return `Object ${objectIndex + 1}`

  }

  return value

    .replace(

      /[_-]+/g,

      ' '

    )

    .replace(

      /\b\w/g,

      (character) => {

        return character.toUpperCase()

      }

    )

}

const parseSvgLength = (

  value: string | null

) => {

  if (!value) {

    return null

  }

  const parsedValue =

    Number.parseFloat(value)

  if (

    !Number.isFinite(parsedValue)

    || parsedValue <= 0

  ) {

    return null

  }

  return parsedValue

}

// ============================================================

// Draft preview

// ============================================================

const collapseDraft = (

  item: GalleryItem

) => {

  item.draftExpanded =

    false

}

const expandDraft = (

  item: GalleryItem

) => {

  item.draftExpanded =

    true

}

// ============================================================

// SVG sanitization

// ============================================================

const sanitizeSvg = (

  svgRoot: SVGSVGElement

) => {

  svgRoot

    .querySelectorAll(

      'script, foreignObject'

    )

    .forEach((element) => {

      element.remove()

    })

  const allElements: Element[] = [

    svgRoot,

    ...Array.from(

      svgRoot.querySelectorAll('*')

    )

  ]

  allElements.forEach((element) => {

    Array.from(

      element.attributes

    ).forEach((attribute) => {

      if (

        /^on/i.test(

          attribute.name

        )

      ) {

        element.removeAttribute(

          attribute.name

        )

      }

      if (

        (

          attribute.name === 'href'

          || attribute.name === 'xlink:href'

        )

        && /^\s*javascript:/i.test(

          attribute.value

        )

      ) {

        element.removeAttribute(

          attribute.name

        )

      }

    })

  })

}

// ============================================================

// Dark-mode protection for inline SVG

// ============================================================

const protectSvgFromDarkMode = (

  svgRoot: SVGSVGElement

) => {

  const setImportantStyle = (

    element: SVGElement,

    property: string,

    value: string

  ) => {

    element.style.setProperty(

      property,

      value,

      'important'

    )

  }

  const resolvePresentationValue = (

    element: SVGElement,

    property: 'stroke' | 'fill'

  ) => {

    let currentElement: Element | null =

      element

    while (

      currentElement

      && currentElement !== svgRoot.parentElement

    ) {

      if (

        currentElement

        instanceof SVGElement

      ) {

        const inlineValue =

          currentElement.style

            .getPropertyValue(property)

            .trim()

        if (inlineValue) {

          return inlineValue

        }

        const attributeValue =

          currentElement

            .getAttribute(property)

            ?.trim()

        if (attributeValue) {

          return attributeValue

        }

      }

      currentElement =

        currentElement.parentElement

    }

    return ''

  }

  svgRoot.setAttribute(

    'data-SketchDirector-light-svg',

    'true'

  )

  setImportantStyle(

    svgRoot,

    'background',

    '#ffffff'

  )

  setImportantStyle(

    svgRoot,

    'color',

    '#111111'

  )

  setImportantStyle(

    svgRoot,

    'color-scheme',

    'only light'

  )

  setImportantStyle(

    svgRoot,

    'forced-color-adjust',

    'none'

  )

  setImportantStyle(

    svgRoot,

    'filter',

    'none'

  )

  setImportantStyle(

    svgRoot,

    'mix-blend-mode',

    'normal'

  )

  svgRoot

    .querySelectorAll<SVGElement>(

      '*'

    )

    .forEach((element) => {

      setImportantStyle(

        element,

        'forced-color-adjust',

        'none'

      )

    })

  const graphicTags =

    new Set([

      'path',

      'line',

      'polyline',

      'polygon',

      'rect',

      'circle',

      'ellipse'

    ])

  svgRoot

    .querySelectorAll<SVGElement>(

      'g.stroke-group, g.stroke-group *'

    )

    .forEach((element) => {

      setImportantStyle(

        element,

        'color',

        '#111111'

      )

      setImportantStyle(

        element,

        'filter',

        'none'

      )

      setImportantStyle(

        element,

        'mix-blend-mode',

        'normal'

      )

      setImportantStyle(

        element,

        'opacity',

        '1'

      )

      const tagName =

        element.tagName.toLowerCase()

      if (

        !graphicTags.has(tagName)

      ) {

        return

      }

      const originalStroke =

        resolvePresentationValue(

          element,

          'stroke'

        )

      const normalizedStroke =

        originalStroke

          && originalStroke !== 'currentColor'

          && originalStroke !== 'inherit'

          && originalStroke !== 'none'

          ? originalStroke

          : '#111111'

      setImportantStyle(

        element,

        'stroke',

        normalizedStroke

      )

      const originalFill =

        resolvePresentationValue(

          element,

          'fill'

        )

      if (

        originalFill

        && originalFill !== 'currentColor'

        && originalFill !== 'inherit'

      ) {

        setImportantStyle(

          element,

          'fill',

          originalFill

        )

      } else {

        setImportantStyle(

          element,

          'fill',

          'none'

        )

      }

    })

  svgRoot

    .querySelectorAll<SVGElement>(

      '.white-box-bg, .white-box-bg *'

    )

    .forEach((element) => {

      setImportantStyle(

        element,

        'color',

        '#ffffff'

      )

      setImportantStyle(

        element,

        'fill',

        '#ffffff'

      )

      setImportantStyle(

        element,

        'stroke',

        '#ffffff'

      )

      setImportantStyle(

        element,

        'filter',

        'none'

      )

      setImportantStyle(

        element,

        'mix-blend-mode',

        'normal'

      )

      setImportantStyle(

        element,

        'opacity',

        '1'

      )

    })

}

// ============================================================

// Initial viewBox

// ============================================================

const ensureInitialViewBox = (

  svgRoot: SVGSVGElement

) => {

  const currentViewBox =

    svgRoot.getAttribute('viewBox')

  if (

    currentViewBox

    && currentViewBox.trim()

  ) {

    return

  }

  const sourceWidth =

    parseSvgLength(

      svgRoot.getAttribute('width')

    )

    ?? DEFAULT_SVG_WIDTH

  const sourceHeight =

    parseSvgLength(

      svgRoot.getAttribute('height')

    )

    ?? DEFAULT_SVG_HEIGHT

  svgRoot.setAttribute(

    'viewBox',

    `0 0 ${sourceWidth} ${sourceHeight}`

  )

}

// ============================================================

// SVG group helpers

// ============================================================

const isSvgGroup = (

  element: Element | null

): element is SVGGElement => {

  return Boolean(

    element

    && element.namespaceURI

      === SVG_NAMESPACE

    && element.tagName

      .toLowerCase()

      === 'g'

  )

}

// ============================================================

// Find complete object group

// ============================================================

const findCompleteObjectGroup = (

  semanticGroup: SVGGElement,

  svgRoot: SVGSVGElement

): SVGGElement => {

  let currentElement: Element | null =

    semanticGroup.parentElement

  let fallbackGroup: SVGGElement | null =

    null

  while (

    currentElement

    && currentElement !== svgRoot

  ) {

    if (

      !isSvgGroup(

        currentElement

      )

    ) {

      currentElement =

        currentElement.parentElement

      continue

    }

    const semanticGroupCount =

      currentElement.querySelectorAll(

        'g.stroke-group[id]'

      ).length

    if (

      semanticGroupCount > 1

    ) {

      break

    }

    if (

      semanticGroupCount === 1

    ) {

      fallbackGroup =

        currentElement

      const hasWhiteBackground =

        currentElement.querySelector(

          '.white-box-bg'

        )

      if (

        hasWhiteBackground

      ) {

        return currentElement

      }

    }

    currentElement =

      currentElement.parentElement

  }

  return (

    fallbackGroup

    ?? semanticGroup

  )

}

// ============================================================

// Prefix SVG IDs

// ============================================================

const prefixSvgIds = (

  svgRoot: SVGSVGElement,

  prefix: string

) => {

  const idMap =

    new Map<string, string>()

  const elementsWithId =

    Array.from(

      svgRoot.querySelectorAll<SVGElement>(

        '[id]'

      )

    )

  elementsWithId.forEach(

    (

      element,

      elementIndex

    ) => {

      const originalId =

        element.getAttribute('id')

      if (!originalId) {

        return

      }

      const safeId =

        originalId.replace(

          /[^a-zA-Z0-9_-]/g,

          '-'

        )

      const newId =

        `${prefix}-${safeId}-${elementIndex}`

      idMap.set(

        originalId,

        newId

      )

      element.setAttribute(

        'id',

        newId

      )

    }

  )

  const allElements: Element[] = [

    svgRoot,

    ...Array.from(

      svgRoot.querySelectorAll('*')

    )

  ]

  allElements.forEach((element) => {

    Array.from(

      element.attributes

    ).forEach((attribute) => {

      let newValue =

        attribute.value

      idMap.forEach(

        (

          newId,

          originalId

        ) => {

          newValue =

            newValue

              .split(

                `url(#${originalId})`

              )

              .join(

                `url(#${newId})`

              )

              .split(

                `#${originalId}`

              )

              .join(

                `#${newId}`

              )

        }

      )

      if (

        newValue

        !== attribute.value

      ) {

        element.setAttribute(

          attribute.name,

          newValue

        )

      }

    })

  })

}

// ============================================================

// Prepare editable SVG

// ============================================================

const prepareEditableSvg = (

  svgText: string,

  galleryId: string

) => {

  const parser =

    new DOMParser()

  const svgDocument =

    parser.parseFromString(

      svgText,

      'image/svg+xml'

    )

  const parserError =

    svgDocument.querySelector(

      'parsererror'

    )

  if (parserError) {

    throw new Error(

      'The SVG file could not be parsed.'

    )

  }

  const svgRoot =

    svgDocument.querySelector<SVGSVGElement>(

      'svg'

    )

  if (!svgRoot) {

    throw new Error(

      'The loaded file is not a valid SVG.'

    )

  }

  sanitizeSvg(

    svgRoot

  )

  ensureInitialViewBox(

    svgRoot

  )

  protectSvgFromDarkMode(

    svgRoot

  )

  svgRoot.setAttribute(

    'width',

    '100%'

  )

  svgRoot.setAttribute(

    'height',

    '100%'

  )

  svgRoot.setAttribute(

    'preserveAspectRatio',

    'xMidYMid meet'

  )

  svgRoot.setAttribute(

    'class',

    'editable-scene-svg'

  )

  // ==========================================================

  // Find semantic object groups

  // ==========================================================

  const semanticGroups =

    Array.from(

      svgRoot.querySelectorAll<SVGGElement>(

        'g.stroke-group[id]'

      )

    )

  if (

    semanticGroups.length === 0

  ) {

    throw new Error(

      'No editable object groups were found.'

    )

  }

  const resolvedObjects =

    semanticGroups.map(

      (semanticGroup) => {

        return {

          semanticGroup,

          objectGroup:

            findCompleteObjectGroup(

              semanticGroup,

              svgRoot

            )

        }

      }

    )

  const seenObjectGroups =

    new Set<SVGGElement>()

  const uniqueObjects =

    resolvedObjects.filter(

      (resolvedObject) => {

        if (

          seenObjectGroups.has(

            resolvedObject.objectGroup

          )

        ) {

          return false

        }

        seenObjectGroups.add(

          resolvedObject.objectGroup

        )

        return true

      }

    )

  const objects: EditableObject[] = []

  // ==========================================================

  // Wrap each object

  // ==========================================================

  uniqueObjects.forEach(

    (

      resolvedObject,

      objectIndex

    ) => {

      const {

        semanticGroup,

        objectGroup

      } = resolvedObject

      const semanticId =

        semanticGroup.getAttribute('id')

        ?? `object-${objectIndex + 1}`

      const objectKey =

        String(objectIndex)

      const parentNode =

        objectGroup.parentNode

      if (!parentNode) {

        return

      }

      const editableWrapper =

        svgDocument.createElementNS(

          SVG_NAMESPACE,

          'g'

        )

      editableWrapper.setAttribute(

        'class',

        'editable-object-wrapper'

      )

      editableWrapper.setAttribute(

        'data-object-key',

        objectKey

      )

      editableWrapper.setAttribute(

        'data-object-label',

        prettifyObjectName(

          semanticId,

          objectIndex

        )

      )

      editableWrapper.setAttribute(

        'data-semantic-id',

        semanticId

      )

      parentNode.insertBefore(

        editableWrapper,

        objectGroup

      )

      editableWrapper.appendChild(

        objectGroup

      )

      objects.push({

        key: objectKey,

        label:

          prettifyObjectName(

            semanticId,

            objectIndex

          ),

        x: 0,

        y: 0,

        scale: 1,

        rotation: 0,

        centerX: 0,

        centerY: 0,

        deleted: false

      })

    }

  )

  if (

    objects.length === 0

  ) {

    throw new Error(

      'Editable SVG objects could not be initialized.'

    )

  }

  prefixSvgIds(

    svgRoot,

    galleryId

  )

  const serializer =

    new XMLSerializer()

  return {

    markup:

      serializer.serializeToString(

        svgRoot

      ),

    objects

  }

}

// ============================================================

// DOM lookup

// ============================================================

const getSvgHost = (

  item: GalleryItem

) => {

  return document.querySelector<HTMLElement>(

    `[data-gallery-editor="${item.id}"]`

  )

}

const getSvgElement = (

  item: GalleryItem

) => {

  return (

    getSvgHost(item)

      ?.querySelector<SVGSVGElement>(

        'svg'

      )

    ?? null

  )

}

const getObjectElement = (

  item: GalleryItem,

  objectKey: string

) => {

  const host =

    getSvgHost(item)

  if (!host) {

    return null

  }

  return (

    Array.from(

      host.querySelectorAll<SVGGElement>(

        '.editable-object-wrapper'

      )

    ).find((element) => {

      return (

        element.getAttribute(

          'data-object-key'

        )

        === objectKey

      )

    })

    ?? null

  )

}

// ============================================================

// Object center

// ============================================================

const cacheObjectCenters = (

  item: GalleryItem

) => {

  item.objects.forEach((object) => {

    const objectElement =

      getObjectElement(

        item,

        object.key

      )

    if (!objectElement) {

      return

    }

    try {

      const boundingBox =

        objectElement.getBBox()

      object.centerX =

        boundingBox.x

        + boundingBox.width / 2

      object.centerY =

        boundingBox.y

        + boundingBox.height / 2

    } catch {

      object.centerX = 0

      object.centerY = 0

    }

  })

}

// ============================================================

// Apply transforms

// ============================================================

const applyObjectTransform = (

  item: GalleryItem,

  object: EditableObject

) => {

  const objectElement =

    getObjectElement(

      item,

      object.key

    )

  if (!objectElement) {

    return

  }

  if (

    object.deleted

  ) {

    objectElement.style.display =

      'none'

    return

  }

  objectElement.style.display =

    ''

  const transform = [

    `translate(${object.x} ${object.y})`,

    `translate(${object.centerX} ${object.centerY})`,

    `rotate(${object.rotation})`,

    `scale(${object.scale})`,

    `translate(${-object.centerX} ${-object.centerY})`

  ].join(' ')

  objectElement.setAttribute(

    'transform',

    transform

  )

}

const applyAllObjectTransforms = (

  item: GalleryItem

) => {

  item.objects.forEach((object) => {

    applyObjectTransform(

      item,

      object

    )

  })

}

// ============================================================

// SVG bounds

// ============================================================

const getElementBoundsInRoot = (

  element: SVGGraphicsElement,

  svgRoot: SVGSVGElement

): SvgBounds | null => {

  try {

    const boundingBox =

      element.getBBox()

    const elementScreenMatrix =

      element.getScreenCTM()

    const rootScreenMatrix =

      svgRoot.getScreenCTM()

    if (

      !elementScreenMatrix

      || !rootScreenMatrix

    ) {

      return null

    }

    const screenToRootMatrix =

      rootScreenMatrix.inverse()

    const convertPoint = (

      x: number,

      y: number

    ) => {

      const point =

        svgRoot.createSVGPoint()

      point.x = x

      point.y = y

      const screenPoint =

        point.matrixTransform(

          elementScreenMatrix

        )

      return screenPoint.matrixTransform(

        screenToRootMatrix

      )

    }

    const topLeft =

      convertPoint(

        boundingBox.x,

        boundingBox.y

      )

    const topRight =

      convertPoint(

        boundingBox.x + boundingBox.width,

        boundingBox.y

      )

    const bottomLeft =

      convertPoint(

        boundingBox.x,

        boundingBox.y + boundingBox.height

      )

    const bottomRight =

      convertPoint(

        boundingBox.x + boundingBox.width,

        boundingBox.y + boundingBox.height

      )

    const xValues = [

      topLeft.x,

      topRight.x,

      bottomLeft.x,

      bottomRight.x

    ]

    const yValues = [

      topLeft.y,

      topRight.y,

      bottomLeft.y,

      bottomRight.y

    ]

    return {

      minX:

        Math.min(...xValues),

      minY:

        Math.min(...yValues),

      maxX:

        Math.max(...xValues),

      maxY:

        Math.max(...yValues)

    }

  } catch {

    return null

  }

}

// ============================================================

// Fit SVG scene

// ============================================================

const fitSvgScene = (

  item: GalleryItem

) => {

  const svgRoot =

    getSvgElement(item)

  const host =

    getSvgHost(item)

  if (

    !svgRoot

    || !host

  ) {

    return

  }

  const editableElements =

    Array.from(

      host.querySelectorAll<SVGGraphicsElement>(

        '.editable-object-wrapper'

      )

    ).filter((element) => {

      return (

        element.style.display

        !== 'none'

      )

    })

  if (

    editableElements.length === 0

  ) {

    return

  }

  let sceneMinX =

    Number.POSITIVE_INFINITY

  let sceneMinY =

    Number.POSITIVE_INFINITY

  let sceneMaxX =

    Number.NEGATIVE_INFINITY

  let sceneMaxY =

    Number.NEGATIVE_INFINITY

  editableElements.forEach((element) => {

    const bounds =

      getElementBoundsInRoot(

        element,

        svgRoot

      )

    if (!bounds) {

      return

    }

    sceneMinX =

      Math.min(

        sceneMinX,

        bounds.minX

      )

    sceneMinY =

      Math.min(

        sceneMinY,

        bounds.minY

      )

    sceneMaxX =

      Math.max(

        sceneMaxX,

        bounds.maxX

      )

    sceneMaxY =

      Math.max(

        sceneMaxY,

        bounds.maxY

      )

  })

  if (

    !Number.isFinite(sceneMinX)

    || !Number.isFinite(sceneMinY)

    || !Number.isFinite(sceneMaxX)

    || !Number.isFinite(sceneMaxY)

  ) {

    return

  }

  const sceneWidth =

    sceneMaxX - sceneMinX

  const sceneHeight =

    sceneMaxY - sceneMinY

  if (

    sceneWidth <= 0

    || sceneHeight <= 0

  ) {

    return

  }

  const padding =

    Math.max(

      Math.max(

        sceneWidth,

        sceneHeight

      ) * SVG_FIT_PADDING_RATIO,

      SVG_FIT_MIN_PADDING

    )

  svgRoot.setAttribute(

    'viewBox',

    [

      sceneMinX - padding,

      sceneMinY - padding,

      sceneWidth + padding * 2,

      sceneHeight + padding * 2

    ].join(' ')

  )

  svgRoot.setAttribute(

    'preserveAspectRatio',

    'xMidYMid meet'

  )

}

// ============================================================

// Selection

// ============================================================

const updateSelectionClass = (

  item: GalleryItem

) => {

  const host =

    getSvgHost(item)

  if (!host) {

    return

  }

  host

    .querySelectorAll<SVGGElement>(

      '.editable-object-wrapper'

    )

    .forEach((element) => {

      element.classList.toggle(

        'is-selected',

        element.getAttribute(

          'data-object-key'

        )

        === item.selectedKey

      )

    })

}

const clearSelection = (

  item: GalleryItem

) => {

  item.selectedKey =

    null

  updateSelectionClass(

    item

  )

}

const clearOtherSelections = (

  activeItemId: string

) => {

  galleryItems.forEach((item) => {

    if (

      item.id !== activeItemId

    ) {

      clearSelection(

        item

      )

    }

  })

}

const selectObject = (

  item: GalleryItem,

  objectKey: string

) => {

  clearOtherSelections(

    item.id

  )

  item.selectedKey =

    objectKey

  updateSelectionClass(

    item

  )

  getSvgHost(item)

    ?.focus()

}

// ============================================================

// Selected object

// ============================================================

const getSelectedObject = (

  item: GalleryItem

) => {

  return item.objects.find((object) => {

    return (

      object.key

      === item.selectedKey

      && !object.deleted

    )

  })

}

const getSelectedLabel = (

  item: GalleryItem

) => {

  return (

    getSelectedObject(item)

      ?.label

    ?? 'Select an object'

  )

}

// ============================================================

// Pointer coordinates

// ============================================================

const clientToParentPoint = (

  parentElement: SVGGraphicsElement | SVGSVGElement,

  clientX: number,

  clientY: number

) => {

  // If the editable object is a direct child of the root <svg>,

  // parentElement.ownerSVGElement is null. In that case the parent

  // itself is the coordinate-system root and must be used directly.

  const svgElement =

    parentElement instanceof SVGSVGElement

      ? parentElement

      : parentElement.ownerSVGElement

  if (!svgElement) {

    return null

  }

  const screenMatrix =

    parentElement.getScreenCTM()

  if (!screenMatrix) {

    return null

  }

  try {

    const point =

      svgElement.createSVGPoint()

    point.x =

      clientX

    point.y =

      clientY

    const localPoint =

      point.matrixTransform(

        screenMatrix.inverse()

      )

    return {

      x:

        localPoint.x,

      y:

        localPoint.y

    }

  } catch {

    // A non-invertible SVG transform should not silently become (0, 0),

    // otherwise dragging appears to be enabled but the object never moves.

    return null

  }

}

// ============================================================

// Move one object

// ============================================================

const startObjectMove = (

  event: PointerEvent,

  item: GalleryItem

) => {

  if (

    event.button !== 0

  ) {

    return

  }

  const eventTarget =

    event.target

  if (

    !(eventTarget instanceof Element)

  ) {

    return

  }

  const objectElement =

    eventTarget.closest(

      '.editable-object-wrapper'

    ) as SVGGElement | null

  if (!objectElement) {

    clearSelection(

      item

    )

    return

  }

  const objectKey =

    objectElement.getAttribute(

      'data-object-key'

    )

  if (

    objectKey === null

  ) {

    return

  }

  const object =

    item.objects.find(

      (currentObject) => {

        return (

          currentObject.key

          === objectKey

        )

      }

    )

  if (

    !object

    || object.deleted

  ) {

    return

  }

  const parentElement =

    objectElement.parentElement

  if (

    !parentElement

    || !(

      parentElement instanceof SVGGraphicsElement

      || parentElement instanceof SVGSVGElement

    )

  ) {

    return

  }

  event.preventDefault()

  event.stopPropagation()

  selectObject(

    item,

    objectKey

  )

  stopActiveInteraction?.()

  const startPointer =

    clientToParentPoint(

      parentElement,

      event.clientX,

      event.clientY

    )

  if (!startPointer) {

    return

  }

  const startObjectX =

    object.x

  const startObjectY =

    object.y

  const handlePointerMove = (

    moveEvent: PointerEvent

  ) => {

    moveEvent.preventDefault()

    const currentPointer =

      clientToParentPoint(

        parentElement,

        moveEvent.clientX,

        moveEvent.clientY

      )

    if (!currentPointer) {

      return

    }

    object.x =

      startObjectX

      + currentPointer.x

      - startPointer.x

    object.y =

      startObjectY

      + currentPointer.y

      - startPointer.y

    applyObjectTransform(

      item,

      object

    )

  }

  const stopPointerMove = () => {

    window.removeEventListener(

      'pointermove',

      handlePointerMove

    )

    window.removeEventListener(

      'pointerup',

      stopPointerMove

    )

    window.removeEventListener(

      'pointercancel',

      stopPointerMove

    )

    document.body.style.userSelect =

      ''

    if (

      stopActiveInteraction

      === stopPointerMove

    ) {

      stopActiveInteraction =

        null

    }

  }

  document.body.style.userSelect =

    'none'

  window.addEventListener(

    'pointermove',

    handlePointerMove

  )

  window.addEventListener(

    'pointerup',

    stopPointerMove

  )

  window.addEventListener(

    'pointercancel',

    stopPointerMove

  )

  stopActiveInteraction =

    stopPointerMove

}

// ============================================================

// Rotate

// ============================================================

const rotateSelected = (

  item: GalleryItem,

  degrees: number

) => {

  const object =

    getSelectedObject(item)

  if (!object) {

    return

  }

  object.rotation +=

    degrees

  applyObjectTransform(

    item,

    object

  )

}

// ============================================================

// Scale

// ============================================================

const scaleSelected = (

  item: GalleryItem,

  amount: number

) => {

  const object =

    getSelectedObject(item)

  if (!object) {

    return

  }

  object.scale =

    clamp(

      object.scale

      + amount,

      MIN_SCALE,

      MAX_SCALE

    )

  applyObjectTransform(

    item,

    object

  )

}

// ============================================================

// Delete

// ============================================================

const deleteSelected = (

  item: GalleryItem

) => {

  const object =

    getSelectedObject(item)

  if (!object) {

    return

  }

  object.deleted =

    true

  applyObjectTransform(

    item,

    object

  )

  clearSelection(

    item

  )

}

// ============================================================

// Reset

// ============================================================

const resetScene = (

  item: GalleryItem

) => {

  stopActiveInteraction?.()

  item.objects.forEach((object) => {

    object.x = 0

    object.y = 0

    object.scale = 1

    object.rotation = 0

    object.deleted = false

  })

  applyAllObjectTransforms(

    item

  )

  clearSelection(

    item

  )

  window.requestAnimationFrame(() => {

    fitSvgScene(

      item

    )

  })

}

// ============================================================

// Keyboard controls

// ============================================================

const handleEditorKeydown = (

  event: KeyboardEvent,

  item: GalleryItem

) => {

  const object =

    getSelectedObject(item)

  if (!object) {

    return

  }

  const moveAmount =

    event.shiftKey

      ? 10

      : 3

  switch (

    event.key

  ) {

    case 'Delete':

    case 'Backspace':

      event.preventDefault()

      deleteSelected(

        item

      )

      break

    case 'ArrowLeft':

      event.preventDefault()

      object.x -=

        moveAmount

      applyObjectTransform(

        item,

        object

      )

      break

    case 'ArrowRight':

      event.preventDefault()

      object.x +=

        moveAmount

      applyObjectTransform(

        item,

        object

      )

      break

    case 'ArrowUp':

      event.preventDefault()

      object.y -=

        moveAmount

      applyObjectTransform(

        item,

        object

      )

      break

    case 'ArrowDown':

      event.preventDefault()

      object.y +=

        moveAmount

      applyObjectTransform(

        item,

        object

      )

      break

  }

}

// ============================================================

// Load SVG

// ============================================================

const loadGalleryItem = async (

  item: GalleryItem

) => {

  item.loading =

    true

  item.error =

    ''

  try {

    const response =

      await fetch(

        item.svg

      )

    if (

      !response.ok

    ) {

      throw new Error(

        `Unable to load ${item.svg}`

      )

    }

    const svgText =

      await response.text()

    const preparedSvg =

      prepareEditableSvg(

        svgText,

        item.id

      )

    item.svgMarkup =

      preparedSvg.markup

    item.objects =

      preparedSvg.objects

    item.loading =

      false

    await nextTick()

    await waitForNextFrame()

    await waitForNextFrame()

    cacheObjectCenters(

      item

    )

    applyAllObjectTransforms(

      item

    )

    await waitForNextFrame()

    fitSvgScene(

      item

    )

  } catch (error) {

    item.error =

      error instanceof Error

        ? error.message

        : 'Failed to load the SVG scene.'

    item.loading =

      false

  }

}

// ============================================================

// Lifecycle

// ============================================================

onMounted(() => {

  galleryItems.forEach((item) => {

    loadGalleryItem(item)

  })

})

onBeforeUnmount(() => {

  stopActiveInteraction?.()

})

</script>

<template>

  <section class="examples-section">

    <el-divider />

    <!-- ====================================================== -->

    

    <!-- ====================================================== -->

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="23"

        :md="22"

        :lg="22"

        :xl="21"

      >

        <div class="examples-shell">


      <!-- ==================================================== -->

      <!-- Interaction hint + More results -->

      <!-- ==================================================== -->

      <div class="gallery-interaction-row">

        <p class="gallery-interaction-tip">

          <span

            class="gallery-interaction-icon"

            aria-hidden="true"

          >

            ✦

          </span>

          <span

            class="gallery-interaction-icon"

            aria-hidden="true"

          >

            Click and drag individual objects to experience object-level sketch editing.

          </span>

        </p>

        <a

          class="more-results-button"

          :href="moreResultsUrl"

          aria-label="View more SketchDirector results"

        >

          <span

            class="more-results-arrow"

            aria-hidden="true"

          >
            More results →

          </span>

        </a>

      </div>

      <!-- ==================================================== -->

      <!-- Three examples shown simultaneously -->

      <!-- ==================================================== -->

      <div class="examples-grid">

        <article

          v-for="(item, itemIndex) in galleryItems"

          :key="item.id"

          class="example-column"

        >

          <!-- ----------------------------------------------- -->

          <!-- Example heading -->

          <!-- ----------------------------------------------- -->

          <div class="example-copy">

            <div class="example-heading-row">

              <h3 class="example-label">

                {{ item.title }}

              </h3>

              <span class="example-index">

                {{

                  String(

                    itemIndex + 1

                  ).padStart(

                    2,

                    '0'

                  )

                }}

              </span>

            </div>

            <el-tooltip

              :content="item.caption"

              placement="top"

              effect="light"

              :show-after="180"

              :hide-after="0"

              popper-class="example-caption-tooltip"

            >

              <p

                class="example-caption"

                tabindex="0"

              >

                “{{ item.caption }}”

              </p>

            </el-tooltip>

          </div>

          <!-- ----------------------------------------------- -->

          <!-- Editable SVG card -->

          <!-- ----------------------------------------------- -->

          <div class="gallery-card">

            <div class="detail-stage">

              <div

                v-if="item.loading"

                class="gallery-state"

              >

                <el-skeleton

                  animated

                  :rows="5"

                />

              </div>

              <div

                v-else-if="item.error"

                class="gallery-state gallery-error"

              >

                {{ item.error }}

              </div>

              <div

                v-else

                class="svg-host"

                :data-gallery-editor="item.id"

                tabindex="0"

                role="application"

                :aria-label="`${item.title} editable SVG scene`"

                v-html="item.svgMarkup"

                @pointerdown="

                  startObjectMove(

                    $event,

                    item

                  )

                "

                @keydown="

                  handleEditorKeydown(

                    $event,

                    item

                  )

                "

              />

              <span

                v-if="

                  !item.loading

                  && !item.error

                "

                class="detail-label"

              >

                Editable SVG

              </span>

            </div>

            <div

              v-if="

                !item.loading

                && !item.error

              "

              class="object-toolbar"

            >

              <div class="selection-info">

                <span class="selection-label">

                  Selected

                </span>

                <span class="selection-name">

                  {{ getSelectedLabel(item) }}

                </span>

              </div>

              <div class="toolbar-actions">

                <el-button

                  circle

                  size="small"

                  title="Rotate left"

                  :disabled="!getSelectedObject(item)"

                  @click="

                    rotateSelected(

                      item,

                      -15

                    )

                  "

                >

                  <el-icon>

                    <RefreshLeft />

                  </el-icon>

                </el-button>

                <el-button

                  circle

                  size="small"

                  title="Rotate right"

                  :disabled="!getSelectedObject(item)"

                  @click="

                    rotateSelected(

                      item,

                      15

                    )

                  "

                >

                  <el-icon>

                    <RefreshRight />

                  </el-icon>

                </el-button>

                <el-button

                  circle

                  size="small"

                  title="Scale down"

                  :disabled="!getSelectedObject(item)"

                  @click="

                    scaleSelected(

                      item,

                      -0.1

                    )

                  "

                >

                  <el-icon>

                    <ZoomOut />

                  </el-icon>

                </el-button>

                <el-button

                  circle

                  size="small"

                  title="Scale up"

                  :disabled="!getSelectedObject(item)"

                  @click="

                    scaleSelected(

                      item,

                      0.1

                    )

                  "

                >

                  <el-icon>

                    <ZoomIn />

                  </el-icon>

                </el-button>

                <el-button

                  circle

                  plain

                  size="small"

                  type="danger"

                  title="Delete object"

                  :disabled="!getSelectedObject(item)"

                  @click="

                    deleteSelected(item)

                  "

                >

                  <el-icon>

                    <Delete />

                  </el-icon>

                </el-button>

                <el-button

                  circle

                  size="small"

                  title="Reset scene"

                  @click="

                    resetScene(item)

                  "

                >

                  <el-icon>

                    <Refresh />

                  </el-icon>

                </el-button>

              </div>

            </div>

          </div>

        </article>

      </div>

        </div>

        
      </el-col>

      
    </el-row>

  </section>


</template>

<style scoped>
.examples-section { width:100%; background:#faf9f7; color:#191d20; padding:32px 0 70px; font-family: Inter, 'Helvetica Neue', Arial, sans-serif;}
.examples-section :deep(.el-divider) { border-color:#e9e8e6; margin:0 0 48px; }
.examples-shell { width:100%; margin:0 auto; max-width:1450px; box-sizing:border-box; }
.overview-card { margin:0 auto 35px; padding:0 0 28px; max-width:1050px; text-align:center; border-bottom:1px solid #e7e5e2; background:none; box-shadow:none; }
.overview-title { margin:0; color:#202427; font-size:clamp(20px,2.1vw,30px); line-height:1.45; font-weight:600; letter-spacing:-.035em; }
.gallery-interaction-row { display:flex; align-items:center; justify-content:space-between; gap:20px; margin:0 0 28px; }
.gallery-interaction-tip { display:flex; align-items:center; gap:10px; margin:0; color:#656a6e; font-size:15px; line-height:1.6; }
.gallery-interaction-icon { font-size:25px; color:#7769cb; }
.more-results-button { display:inline-flex; justify-content:center; align-items:center; gap:12px; flex-shrink:0; border:1px solid #242729; border-radius:999px; background:transparent; padding:11px 20px; color:#191d20; text-decoration:none; font-size:14px; font-weight:650; transition:background .2s,color .2s; }
.more-results-button:hover { background:#191d20; color:#fff; }
.more-results-arrow { font-size:25px; }
.examples-grid { display:grid; grid-template-columns:repeat(3,minmax(0,1fr)); gap:24px; align-items:stretch; }
.example-column { min-width:0; display:flex; flex-direction:column; }
.example-copy { min-height:115px; padding:0 2px 12px; }
.example-heading-row { display:flex; justify-content:space-between; align-items:center; gap:12px; margin-bottom:8px; }
.example-label { margin:0; font-size:30px; font-weight:700; color:#191d20; }
.example-index { color:#aaa; font-size:30px; letter-spacing:.1em; font-weight:600; }
.example-caption { margin:0; color:#666c71; font-size:23px; line-height:1.55; display:-webkit-box; -webkit-line-clamp:3; -webkit-box-orient:vertical; overflow:hidden; cursor:help; }
.gallery-card { display:flex; flex-direction:column; width:100%; margin-top:auto; border:1px solid #e0e0df; border-radius:12px; overflow:hidden; background:#fff; box-sizing:border-box; }
.detail-stage { position:relative; width:100%; aspect-ratio:1 / 1; background:#fff; overflow:hidden; }
.svg-host { position:relative; width:100%; height:100%; background:#fff; overflow:hidden; outline:none; touch-action:none; cursor:grab; }
.svg-host:active { cursor:grabbing; }
.svg-host :deep(svg) { display:block; width:100%; height:100%; }
.svg-host:focus-visible { outline:2px solid #7769cb; outline-offset:-3px; }
.svg-host :deep(.editable-object-wrapper) { cursor:grab; }
.svg-host :deep(.editable-object-wrapper:hover) { filter:drop-shadow(0 0 2px rgba(119,105,203,.5)); }
.svg-host :deep(.editable-object-wrapper.is-selected) { filter:drop-shadow(0 0 3px rgba(119,105,203,.85)); }
.gallery-state { display:flex; align-items:center; justify-content:center; width:100%; height:100%; padding:20px; box-sizing:border-box; }
.gallery-error { color:#aa3737; font-size:13px; text-align:center; }
.detail-label { position:absolute; right:12px; bottom:12px; background:rgba(255,255,255,.93); color:#666; border:1px solid #e4e4e4; border-radius:999px; padding:6px 11px; font-size:11px; font-weight:600; pointer-events:none; }
.object-toolbar { display:flex; flex-direction:column; gap:11px; min-height:89px; background:#fff; border-top:1px solid #ebebeb; padding:14px; box-sizing:border-box; }
.selection-info { display:flex; align-items:center; justify-content:space-between; min-width:0; gap:12px; }
.selection-label { font-size:20px; color:#888; }
.selection-name { font-size:20px; font-weight:650; color:#242729; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; }
.toolbar-actions { display:flex; align-items:center; justify-content:center; flex-wrap:wrap; gap:7px; }
.toolbar-actions :deep(.el-button + .el-button) { margin-left:0; }
.toolbar-actions :deep(.el-button--small.is-circle) { width:33px; height:33px; border:1px solid #d9d9d9; color:#292d30; background:#fff; }
.toolbar-actions :deep(.el-button--small.is-circle:hover:not(:disabled)) { border-color:#1d2327; background:#1d2327; color:#fff; }
.toolbar-actions :deep(.el-button.is-disabled) { opacity:.45; }
@media(max-width:1000px) { .examples-grid {grid-template-columns:repeat(2,minmax(0,1fr));} .example-copy {min-height:110px;} }
@media(max-width:680px) { .examples-section {padding:20px 0 45px;} .examples-grid {grid-template-columns:1fr;gap:30px;} .gallery-interaction-row {align-items:flex-start; flex-direction:column;} .overview-title {font-size:21px;} .example-copy {min-height:0;padding-bottom:16px;} .example-caption {-webkit-line-clamp:unset;} }
:global(html.dark .examples-section), :global(body.dark .examples-section) { color-scheme:light; background:#faf9f7; }
:global(html.dark .examples-section .gallery-card), :global(body.dark .examples-section .gallery-card) {background:#fff;color-scheme:light;}
</style>
