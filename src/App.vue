<template>
  <div id="map" style="width:100%;height:100vh"></div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import planSheets from '@/utils/data/Plansheets.json'
import markers from '@/utils/data/Markers.json'

let map, overlay, AdvancedMarkerElement
const gMarkers = []
const isCalibrated = ref(false)

onMounted(async () => {
  await window.google.maps.ready
  const { Map } = await google.maps.importLibrary('maps')
  const markerLib = await google.maps.importLibrary('marker')
  AdvancedMarkerElement = markerLib.AdvancedMarkerElement

  initMap(Map)
  addOverlay()
  addMarkers()
})

function initMap(Map) {
  map = new Map(document.getElementById('map'), {
    center: { lat: 34.33, lng: -79.44 },
    zoom: 16,
    mapId: 'DEMO_MAP_ID'
  })
}

function addMarkers() {
  markers.forEach(m => {
    const marker = new AdvancedMarkerElement({
      position: { lat: m.latitude, lng: m.longitude },
      map,
      content: makeSvg(m.angle || 0)   // ← HTMLElement چرخان
    })
    gMarkers.push(marker)
  })
}

function makeSvg(rot, calibrated = false) {
  const color = calibrated ? '#ff5722' : '#4285F4'
  const div = document.createElement('div')
  div.innerHTML = `
    <svg width="40" height="40" viewBox="0 0 40 40"
         style="transform:rotate(${rot}deg); transform-origin:50% 50%; pointer-events:none;">
      <path d="M20 4 L36 32 H4 Z" fill="${color}" stroke="#fff" stroke-width="1.5"/>
      <line x1="20" y1="4" x2="20" y2="36" stroke="#fff" stroke-width="2"/>
    </svg>`
  return div.firstElementChild
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