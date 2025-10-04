<template>
  <div id="map" style="width:100%;height:100vh"></div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import planSheets from '@/utils/data/Plansheets.json'

let map, overlay

onMounted(async () => {
  await window.google.maps.ready
  const { Map } = await google.maps.importLibrary('maps')

  initMap(Map)
  addOverlay()
})

function initMap(Map) {
  map = new Map(document.getElementById('map'), {
    center: { lat: 34.33, lng: -79.44 },
    zoom: 16,
    mapId: 'DEMO_MAP_ID'
  })
}

function addOverlay() {
  const sheet = planSheets.find(p => p.mapOverLay)
  if (!sheet) return
  const b = sheet.mapOverLay.planSheetBound
  const bounds = new google.maps.LatLngBounds(
      new google.maps.LatLng(b.bottomLeft.latitude, b.bottomLeft.longitude),
      new google.maps.LatLng(b.topRight.latitude, b.topRight.longitude)
  )
  overlay = new google.maps.GroundOverlay(sheet.url, bounds)
  overlay.setMap(map)
  map.fitBounds(bounds)
}
</script>