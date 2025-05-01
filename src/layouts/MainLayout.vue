<template>
  <Suspense>
    <q-layout view="hHh Lpr lff">
      <q-header elevated>
        <q-toolbar>
          <q-btn flat dense round icon="menu" aria-label="Menu" @click="toggleLeftDrawer" />

          <q-toolbar-title>
            Liber Map
          </q-toolbar-title>
        </q-toolbar>
      </q-header>

      <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
        <q-scroll-area class="fit">
          <q-list>
            <q-expansion-item v-for="gml in gmlList" :key="gml.sha" :label="gml.name" v-bind="gml">
              <template v-slot:default>
                <div style="padding-left: 21.6px; ">
                  <q-list style="border-left: 1px solid rgba(0, 0, 0, .12);">
                    <q-item clickable v-ripple v-for="gmlChild in gml.child" :key="gmlChild.sha" v-bind="gmlChild"
                      @click="console.log(gmlChild.url)">
                      <q-item-section>{{ cleanPath(gmlChild.name) }}</q-item-section>
                    </q-item>
                  </q-list>
                </div>
              </template>
            </q-expansion-item>
          </q-list>
        </q-scroll-area>
      </q-drawer>

      <q-page-container>
        <router-view />
      </q-page-container>
    </q-layout>
  </Suspense>
</template>

<script setup>
import { ref, provide } from 'vue'
// import EssentialLink from 'components/EssentialLink.vue'
import { CONFIG } from "src/keys";

async function fetchConfig() {
  try {
    const url = import.meta.env.BASE_URL + "configs/webapp/config.json";
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    console.error("Error fetching config:", error);
  }
}

const config = await fetchConfig();
provide(CONFIG, config);

async function fetchGml() {
  try {
    const response = await fetch(config.GITHUB_API_URL);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    var mainGml = await response.json();
    mainGml.forEach(async (gml) => {
      const childResponse = await fetch(gml.url);
      const childJson = await childResponse.json();
      var childGml = childJson;
      gml.child = childGml;
    });
    return mainGml
  } catch (error) {
    console.error("Error fetching config:", error);
  }
}
const gmlList = ref(await fetchGml());

const leftDrawerOpen = ref(false)

function toggleLeftDrawer() {
  leftDrawerOpen.value = !leftDrawerOpen.value
}

function cleanPath(path) {
  return path.replace(/[[\]]/g, '').replace(/\.kml$/, ''); // Remove [,], and .kml
}

function decodeBase64ToUTF8(base64) {
  // Decode Base64 to binary string
  const binaryString = atob(base64);

  // Convert binary string to UTF-8 bytes
  const bytes = new Uint8Array(binaryString.length);
  for (let i = 0; i < binaryString.length; i++) {
    bytes[i] = binaryString.charCodeAt(i);
  }

  // Decode UTF-8 bytes to string
  return new TextDecoder('utf-8').decode(bytes);
}

// Example usage
const base64Data = "5LiA5LqM5Lit5paH"; // Base64 for "一二三四"
const decodedContent = decodeBase64ToUTF8(base64Data);

console.log(decodedContent); // Output: "一二三四"

</script>
