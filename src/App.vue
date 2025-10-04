<template>
  <div id="map" style="width:100%;height:100vh"></div>
</template>

<script setup>
import { onMounted, ref } from 'vue'

let map

onMounted(async () => {
  await window.google.maps.ready
  const { Map } = await google.maps.importLibrary('maps')

  initMap(Map)
})

function initMap(Map) {
  map = new Map(document.getElementById('map'), {
    center: { lat: 34.33, lng: -79.44 },
    zoom: 16,
    mapId: 'DEMO_MAP_ID'
  })
}
</script>