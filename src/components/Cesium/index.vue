<script lang="ts" setup>
import { useTemplateRef, onMounted, onUnmounted } from 'vue'
// import * as CESIUM from 'cesium'
// import * as CESIUM from '@/Cesium/index'

import * as CESIUM from '../../Cesium/index.js'

const cesiumLayout = useTemplateRef<HTMLDivElement>('cesiumLayout')

CESIUM.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI2MWI5Mjc4Ni0zYjE0LTRjZDYtODIyNi05ZDAxZDJmZTUxNDQiLCJpZCI6MTY0MzMxLCJpYXQiOjE2OTM4MTc0NTJ9.fV9wIV4QqNLpsMBJOuHIZrWiITp-3rQKvwHyxBrvyKI'
CESIUM.Camera.DEFAULT_VIEW_RECTANGLE = CESIUM.Rectangle.fromDegrees(89.5, 20.4, 110.4, 61.2)

// CESIUM.CesiumWidget.prototype.creditContainer.style.display = 'none'
//初始化场景
let viewer: CESIUM.Viewer | null = null
function initViewer() {
  if (!cesiumLayout.value) return
  viewer = new CESIUM.Viewer('Cesium', {
    contextOptions: { webgl: { alpha: true } },
    shadows: false,
    targetFrameRate: 60,
    infoBox: false,
    geocoder: false,
    homeButton: false,
    sceneModePicker: false,
    baseLayerPicker: false,
    navigationHelpButton: false,
    animation: false,
    timeline: false,
    fullscreenButton: false,
    requestRenderMode: true,
    maximumRenderTimeChange: 100,
    terrainProvider: undefined,
    selectionIndicator: false
    // sceneMode: CESIUM.SceneMode.SCENE2D
  })
  viewer.scene.globe.depthTestAgainstTerrain = true
  // viewer.scene.globe.enableLighting = true
  ;(<HTMLElement>viewer.cesiumWidget.creditContainer).style.display = 'none'
}

let dujiangyanjz: CESIUM.PrimitiveCollection | null = null
let add3dtitles = async function (data: string) {
  CESIUM.Cesium3DTileset.fromUrl(data).then((model) => {
    dujiangyanjz = viewer?.scene.primitives.add(model)
  })
}

onMounted(() => {
  initViewer()
  add3dtitles('/modelapi/tileset.json')
})

onUnmounted(() => {
  if (dujiangyanjz) !dujiangyanjz.isDestroyed && dujiangyanjz.destroy()
})
</script>

<template>
  <div ref="cesiumLayout" id="Cesium" class="cesiumLayout"></div>
</template>

<style scoped>
.cesiumLayout {
  width: 100vw;
  height: 100vh;
}
</style>
