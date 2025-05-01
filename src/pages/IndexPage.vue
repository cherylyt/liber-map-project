<template>
  <div id="map" style="width: 100%; height: 94vh;"></div>
</template>

<script setup>
import { onMounted, ref, provide, inject } from 'vue';
import Map from 'ol/Map.js';
import OSM from 'ol/source/OSM.js';
import TileLayer from 'ol/layer/Tile.js';
import View from 'ol/View.js';
import { fromLonLat } from 'ol/proj';
import 'ol/ol.css'; // Import OpenLayers CSS
import { CONFIG } from "src/keys";

const map = ref(null);
const config = inject(CONFIG);

onMounted(() => {
  map.value = new Map({
    target: 'map',
    layers: [
      new TileLayer({
        source: new OSM(),
      }),
    ],
    view: new View({
      center: fromLonLat(config.HONG_KONG_CENTER),
      zoom: config.DEFAULT_ZOOM,
    }),
  });
});
// Provide the map as a global constant
provide('globalMap', map);
// provide('CONFIG', config);

</script>