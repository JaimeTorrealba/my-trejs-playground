<script setup>
import { shallowRef, reactive } from 'vue'
import { OrbitControls, useTweakPane } from '@tresjs/cientos'
import { useTexture, TresCanvas } from '@tresjs/core'
import { EquirectangularReflectionMapping } from 'three'
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader'

const { map } = await useTexture({map:'/textures/glassMorphismTexture.jpg'})
const { map:normalMap } = await useTexture({map:'/textures/glassMorphismNormal.jpg'})
const planeRef = shallowRef(null)

const hdrEquirect = await new RGBELoader().load(
    "/textures/empty_warehouse_01_2k.hdr",
    () => {
      hdrEquirect.mapping = EquirectangularReflectionMapping;
    }
  );

  const options = reactive({
    transmission: 1,
    thickness: 0.5,
    roughness: 0,
    envMap: hdrEquirect,
    clearcoatNormalMap: normalMap,
    envMapIntensity: 1.5,
    clearcoat: 0.5,
    clearcoatRoughness: 0.1,
    clearcoatNormalScale: 0.3,
  })

  const { pane } = useTweakPane()

  pane.addInput(options, 'transmission', {
  label: 'transmission',
  min: 0,
  max: 1,
  step: 0.01,
})
pane.addInput(options, 'thickness', {
  label: 'thickness',
  min: 0,
  max: 1,
  step: 0.01,
})
  pane.addInput(options, 'envMapIntensity', {
  label: 'envMapIntensity',
  min: 0,
  max: 3,
  step: 0.1,
})
  pane.addInput(options, 'roughness', {
  label: 'roughness',
  min: 0,
  max: 1,
  step: 0.01,
})
  pane.addInput(options, 'clearcoat', {
  label: 'clearcoat',
  min: 0,
  max: 1,
  step: 0.01,
})
  pane.addInput(options, 'clearcoatRoughness', {
  label: 'clearcoatRoughness',
  min: 0,
  max: 1,
  step: 0.01,
})
pane.addInput(options, 'clearcoatNormalScale', {
  label: 'clearcoatNormalScale',
  min: 0,
  max: 1,
  step: 0.01,
})


const hdrEquiredButton = pane.addButton({
  title: 'Set hdrEquired',
});
hdrEquiredButton.on('click', () => {
    options.envMap = options.envMap ? null : hdrEquirect
});

</script>
<template>
  <TresCanvas window-size clear-color="#F7F7F7" class="over-hidden"
  grid >
    <TresPerspectiveCamera :position="[0, 0, 3]" :fov="45" :aspect="1" :near="0.1" :far="1000" />
    <OrbitControls />
    <TresGridHelper :args="[30, 30]" :position="[0, -2.5, 0]" />
    <TresMesh :position="[-0, 0, 0]">
        <TresIcosahedronGeometry :args="[1, 10]" />
        <TresMeshPhysicalMaterial v-bind="options" />
    </TresMesh>
    <TresMesh ref="planeRef" :position="[0,0, -1]">
        <TresPlaneGeometry :args="[5, 5]" />
        <TresMeshBasicMaterial :map="map"  />
    </TresMesh>
    <TresDirectionalLight :position="[0, 2, 4]" :intensity="2" cast-shadow />
    <TresAmbientLight />
  </TresCanvas>
</template>