<script setup>
import { ref, onMounted, watch } from 'vue'
import { debounce } from 'lodash'
import axios from 'axios'
import L from 'leaflet'
import CityCountyData from '@/assets/CityCountyData.json'
import PharmacyDetail from './PharmacyDetail.vue'

const base_url = import.meta.env.VITE_API_BASE_URL
const cites = ref([])
const areas = ref([])
const selectedCity = ref("")
const selectedArea = ref("")
const pharmacyData = ref("")
const keyword = ref("")
const selectedPharmacy = ref(null)
const showPharmacyDetail = ref(false)

let marker = null
let map = null
const redIcon = L.icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-red.png',
  shadowUrl: 'https://unpkg.com/leaflet@1.9.4/dist/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41], 
  popupAnchor: [1, -34],
  shadowSize: [41, 41]
})

onMounted(() => {
  cites.value = CityCountyData
  const personWalkIcon = L.divIcon({
  html: '<i class="fa-solid fa-person-walking fa-3x" style="color:blue"></i>',
  className: 'custom-marker',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [0, -30],
  })
  
  map = L.map('map', {
    center: [24.8045268, 120.9658546],
    zoom: 19,
  })

  L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
      maxZoom: 20,
  }).addTo(map)


  marker = L.marker([24.8045268, 120.9658546], { icon: personWalkIcon }).addTo(map).bindPopup(function (layer) {
      return `<div><p>我在這!</p></div>`
    }).openPopup()

})

watch (selectedCity, (newCity) => {
  if (newCity) {
    const city = cites.value.find((city) => city.CityName === newCity)
    areas.value = city.AreaList
  } else {
    areas.value = []
  }
})

watch (selectedArea, async (newArea) => {
  let result = null
  const response = await fetch(base_url)
  const data = await response.json()
  const filteredPharmacies = data.features.filter(item => item.properties.town === selectedArea.value && item.properties.county === selectedCity.value)
  if (filteredPharmacies.length > 0) {
    pharmacyData.value = filteredPharmacies
    result = {
      "type": "FeatureCollection",
      "features": filteredPharmacies
    }
  }  
  if (marker) {
    marker.remove()
  }
  if (result) {
    map.eachLayer(function(layer) {
    if (layer instanceof L.Marker || layer instanceof L.GeoJSON) {
      map.removeLayer(layer)
    }
  })

  const geoJsonLayer = L.geoJSON(result, {
    pointToLayer: function(feature, latlng) {
      return L.marker(latlng, { icon: redIcon })
    },
  }).bindPopup(function (layer) {
      const feature = layer.feature
      const properties = feature.properties || {}
      return `<div>
        <h3 class="text-2xl mb-2 font-bold">${properties.name}</h3>
        <span class="text-sm text-red-500 font-bold block">口罩剩餘: 成人 - ${properties.mask_adult} 個 / 兒童 - ${properties.mask_child} 個</span>
        <span class="text-xs block">地址: ${properties.address}</span>
        <span class="text-xs block">電話: ${properties.phone}</span>
        <span class="text-xs block">最後更新時間: ${properties.updated}</span>
        </div>`
    }).addTo(map);
    map.fitBounds(geoJsonLayer.getBounds())

  } else {
    pharmacyData.value = null
  }  
})

watch(keyword, async (newKeyword) => {
  const validateKeyword = (value) => {
    const regex = /^[\u4e00-\u9fff]+$/
    if (value.trim() === "" || !regex.test(value)) {
      return true
    } else {
      return false
    }
  }
  
  const debounceValidate = debounce(validateKeyword, 1000)
  if (!debounceValidate(newKeyword)) {
    debounceValidate(newKeyword)
    const response = await fetch(base_url)
    const data = await response.json()
    const filteredPharmacies = data.features.filter(item => item.properties.name.includes(keyword.value))
    pharmacyData.value = filteredPharmacies
  } else {
    pharmacyData.value = []
  }
})

const openPharmacyDetail = (pharmacy) => {
  selectedPharmacy.value = pharmacy
  showPharmacyDetail.value = true
}

const closePharmacyDetail = () => {
  showPharmacyDetail.value = false
  selectedPharmacy.value = null
}

const showPharmacyOnMap = (pharmacy) => {
  if (marker) {
    marker.remove();
  }

  const [ lat, lng ] = pharmacy.geometry.coordinates
  marker = L.marker([lng, lat], { icon: redIcon }).addTo(map).bindPopup(function (layer) {
      return `<div><p>${pharmacy.properties.name}</p></div>`
    }).openPopup()
  map.setView([lng, lat], 18)
  showPharmacyDetail.value = false
}
</script>

<template>
  <div class="container flex flex border-2 border-green-400">
    <div class="aside w-3/12 border-2 border-blue-300">
      <div class="aside-container">

        <div class="select-block border border-b-black bg-gray-300 py-3 pl-2">  

          <div class="select-city flex ">
            <p class="w-1/3">縣市: </p>
            <select id="cityName" v-model="selectedCity" class="w-1/3 bg-white rounded-sm pl-1">
              <option value="">請選擇縣市</option>
              <option v-bind:value="c.CityName" v-for="c in cites" v-bind:key="c.CityName">
                {{ c.CityName }}
              </option>
            </select>
          </div>

          <div class="select-area flex mt-3 ">
            <p class="w-1/3">行政區: </p>
            <select id="areaName" v-model="selectedArea" class="w-1/3 bg-white rounded-sm pl-1">
              <option value="">請選擇區域</option>
              <option v-bind:value="a.AreaName" v-for="a in areas" v-bind:key="a.AreaName">
                {{ a.AreaName }}
              </option>
            </select>
          </div>

        </div>

        <div class="search-block border border-b-black bg-gray-300 pt-3 pb-4 pl-2">

          <label for="search-input"><i class="fas fa-search" style="color: #74C0FC;"></i> 關鍵字搜尋:
            <input type="text" id="search-input" class="block pl-2 mt-1 rounded-sm hover:border-orange-500 hover:bg-blue-100" placeholder="請輸入關鍵字" v-model="keyword">
          </label>
        </div>

        <div class="pharmacy-list border-2 border-orange-400 max-h-96 overflow-auto" v-if="pharmacyData.length > 0">

          <div class="pharmacy-item snap-center border-b-black" v-for="p in pharmacyData" :key="p.id">
            <div class="content flex">
              <div class="text  w-2/3 pl-2">
                <h3 class="my-3 ">{{ p.properties.name }}</h3>
                <p><i class="fa-regular fa-user" style="color: #FFD43B;"></i> 大人口罩:{{ p.properties.mask_adult }} 個</p>
                <p><i class="fa-solid fa-baby" style="color: #63E6BE;"></i> 小孩口罩:{{ p.properties.mask_child }}  個</p>
              </div>
              
              <div class="info-btn flex w-1/3 pt-4 ">
                <button type="button" v-on:click="openPharmacyDetail(p)" class="mx-auto h-16 w-16 bg-gray-300 rounded-sm">
                  <i class="fa-solid fa-circle-exclamation text-2xl" style="color: #74C0FC;"></i>
                <span class="block text-xs">看詳細資訊</span></button>
              </div>
                
            </div>
            <p>最後更新時間: {{ p.properties.updated }}</p>
          </div>

        </div>

        <div v-else class="no-pharmacy-message text-center py-4">
          <p>目前沒有藥局資料。</p>
        </div>

      </div>
    </div>
    <div  class="map border-2 w-9/12 border-orange-400">
      <div id="map" class="map-container border-2 border-pink-400 ">

      </div>
    </div>
  </div>

  <PharmacyDetail v-if="selectedPharmacy" :pharmacy="selectedPharmacy" :isVisible="showPharmacyDetail" v-on:close="closePharmacyDetail" v-on:show-on-map="showPharmacyOnMap" />
</template>

<style scoped>
#map {
    position: relative;
    height: 100vh;
  }
</style>
