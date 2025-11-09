<template>
  <div :class="bgClass" class="min-h-screen text-white transition-all duration-500">
    <nav class="backdrop-blur-md bg-white/10 border-b border-white/20 p-4">
      <div class="max-w-7xl mx-auto flex justify-between items-center">
        <div class="flex items-center space-x-2">
          <Cloud :size="32" />
          <h1 class="text-2xl font-bold">ClimaPure</h1>
        </div>
        <div class="flex items-center space-x-4">
          <div class="text-sm">
            {{ currentTime.toLocaleTimeString() }} | {{ currentTime.toLocaleDateString() }}
          </div>
          <button
            @click="toggleDarkMode"
            class="p-2 rounded-lg backdrop-blur-md bg-white/20 hover:bg-white/30 transition-all"
          >
            <Sun v-if="darkMode" :size="20" />
            <Moon v-else :size="20" />
          </button>
        </div>
      </div>
    </nav>

    <div class="w-full mx-auto p-3 sm:p-4 md:p-6 max-w-[1600px]">
      <div class="flex flex-col gap-4 md:gap-6">
        <div class="backdrop-blur-md bg-white/10 rounded-xl p-4 border border-white/20 shadow-2xl">
          <h2 class="text-lg font-semibold mb-3 flex items-center">
            <MapPin :size="20" class="mr-2" />
            Location
          </h2>

          <div
            ref="mapContainer"
            class="rounded-lg h-64 w-full lg:h-96"
            style="background: linear-gradient(to bottom right, #3b82f6, #06b6d4)"
          ></div>

          <div class="mt-3 text-sm space-y-1">
            <p class="opacity-80">📍 {{ location.name }}</p>
            <p class="opacity-60">
              Lat: {{ location.lat.toFixed(4) }}, Lon: {{ location.lon.toFixed(4) }}
            </p>
          </div>

          <button
            @click="getUserLocation"
            class="w-full mt-3 py-2 backdrop-blur-md bg-white/20 hover:bg-white/30 rounded-lg transition-all flex items-center justify-center space-x-2"
          >
            <MapPin :size="16" />
            <span>Use Current Location</span>
          </button>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-2 2xl:grid-cols-3 gap-4 md:gap-6">
          <div class="space-y-4 md:space-y-6">
            <div
              class="backdrop-blur-md bg-white/10 rounded-xl p-4 border border-white/20 shadow-2xl"
            >
              <div class="flex justify-between items-center mb-3">
                <h2 class="text-lg font-semibold flex items-center">
                  <Star :size="20" class="mr-2" />
                  Favorites
                </h2>
                <button
                  @click="addToFavorites"
                  class="text-xs py-1 px-3 backdrop-blur-md bg-white/20 hover:bg-white/30 rounded-lg transition-all"
                >
                  Add Current
                </button>
              </div>
              <div class="space-y-2 max-h-48 overflow-y-auto">
                <div v-if="favorites.length === 0" class="text-sm opacity-60 text-center py-4">
                  No favorites yet
                </div>
                <div
                  v-for="(fav, idx) in favorites"
                  :key="idx"
                  class="flex justify-between items-center p-2 rounded-lg backdrop-blur-md bg-white/10 hover:bg-white/20 cursor-pointer transition-all"
                  @click="selectFavorite(fav)"
                >
                  <span class="text-sm">{{ fav.name }}</span>
                  <button @click.stop="removeFavorite(idx)" class="text-red-400 hover:text-red-300">
                    <Trash2 :size="16" />
                  </button>
                </div>
              </div>
            </div>

            <div
              class="backdrop-blur-md bg-white/10 rounded-xl p-6 border border-white/20 shadow-2xl"
            >
              <div v-if="loading" class="text-center py-8">
                <div
                  class="animate-spin rounded-full h-12 w-12 border-b-2 border-white mx-auto"
                ></div>
                <p class="mt-4">Loading weather data...</p>
              </div>

              <div v-else>
                <div class="flex justify-between items-start mb-6">
                  <div>
                    <h2 class="text-3xl font-bold mb-1">{{ location.name }}</h2>
                    <p class="text-lg opacity-80 capitalize">{{ weather?.description }}</p>
                  </div>
                  <div class="text-right">
                    <div class="text-5xl font-bold">{{ weather?.temp }}°C</div>
                    <div class="text-sm opacity-80">Feels like {{ weather?.feels_like }}°C</div>
                  </div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-3 gap-4">
                  <div class="backdrop-blur-md bg-white/10 rounded-lg p-3">
                    <div class="flex items-center space-x-2 mb-1">
                      <Droplets :size="16" />
                      <span class="text-sm opacity-80">Humidity</span>
                    </div>
                    <div class="text-2xl font-bold">{{ weather?.humidity }}%</div>
                  </div>
                  <div class="backdrop-blur-md bg-white/10 rounded-lg p-3">
                    <div class="flex items-center space-x-2 mb-1">
                      <Wind :size="16" />
                      <span class="text-sm opacity-80">Wind</span>
                    </div>
                    <div class="text-2xl font-bold">{{ weather?.wind_speed }} m/s</div>
                  </div>
                  <div class="backdrop-blur-md bg-white/10 rounded-lg p-3">
                    <div class="flex items-center space-x-2 mb-1">
                      <Gauge :size="16" />
                      <span class="text-sm opacity-80">Pressure</span>
                    </div>
                    <div class="text-2xl font-bold">{{ weather?.pressure }} hPa</div>
                  </div>
                  <div class="backdrop-blur-md bg-white/10 rounded-lg p-3">
                    <div class="flex items-center space-x-2 mb-1">
                      <Cloud :size="16" />
                      <span class="text-sm opacity-80">Clouds</span>
                    </div>
                    <div class="text-2xl font-bold">{{ weather?.clouds }}%</div>
                  </div>
                  <div class="backdrop-blur-md bg-white/10 rounded-lg p-3">
                    <div class="flex items-center space-x-2 mb-1">
                      <Eye :size="16" />
                      <span class="text-sm opacity-80">Visibility</span>
                    </div>
                    <div class="text-2xl font-bold">
                      {{ (weather?.visibility / 1000).toFixed(1) }} km
                    </div>
                  </div>
                  <div class="backdrop-blur-md bg-white/10 rounded-lg p-3">
                    <div class="flex items-center space-x-2 mb-1">
                      <Wind :size="16" />
                      <span class="text-sm opacity-80">AQI</span>
                    </div>
                    <div :class="`text-2xl font-bold ${aqiLabel.color}`">
                      {{ aqiLabel.label }}
                    </div>
                  </div>
                </div>

                <button
                  @click="expandedView = !expandedView"
                  class="w-full mt-4 py-3 backdrop-blur-md bg-white/20 hover:bg-white/30 rounded-lg transition-all font-semibold lg:hidden"
                >
                  {{ expandedView ? 'Hide Details' : 'Show Detailed View' }}
                </button>
              </div>
            </div>

            <transition>
              <div v-if="expandedView" class="space-y-4 lg:hidden">
                <div
                  class="backdrop-blur-md bg-white/10 rounded-xl p-6 border border-white/20 shadow-2xl"
                >
                  <h3 class="text-xl font-semibold mb-4">Temperature Forecast</h3>
                  <div class="h-64">
                    <LineChart :data="forecast" />
                  </div>
                </div>

                <div
                  class="backdrop-blur-md bg-white/10 rounded-xl p-6 border border-white/20 shadow-2xl"
                >
                  <h3 class="text-xl font-semibold mb-4">Air Quality Components</h3>
                  <div class="h-64">
                    <BarChart :data="pollutantData" />
                  </div>
                  <div class="grid grid-cols-2 md:grid-cols-3 gap-3 mt-4">
                    <div
                      v-for="pollutant in pollutantData"
                      :key="pollutant.name"
                      class="backdrop-blur-md bg-white/10 rounded-lg p-3"
                    >
                      <div class="text-sm opacity-80">{{ pollutant.name }}</div>
                      <div class="text-xl font-bold" :style="{ color: pollutant.color }">
                        {{ pollutant.value.toFixed(1) }} µg/m³
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </transition>
          </div>

          <div class="hidden lg:block space-y-4 md:space-y-6">
            <div
              class="backdrop-blur-md bg-white/10 rounded-xl p-6 border border-white/20 shadow-2xl"
            >
              <h3 class="text-xl font-semibold mb-4">Temperature Forecast</h3>
              <div class="h-64">
                <LineChart :data="forecast" />
              </div>
            </div>

            <div
              class="backdrop-blur-md bg-white/10 rounded-xl p-6 border border-white/20 shadow-2xl 2xl:hidden"
            >
              <h3 class="text-xl font-semibold mb-4">Air Quality Components</h3>
              <div class="h-64">
                <BarChart :data="pollutantData" />
              </div>
              <div class="grid grid-cols-2 md:grid-cols-3 gap-3 mt-4">
                <div
                  v-for="pollutant in pollutantData"
                  :key="pollutant.name"
                  class="backdrop-blur-md bg-white/10 rounded-lg p-3"
                >
                  <div class="text-sm opacity-80">{{ pollutant.name }}</div>
                  <div class="text-xl font-bold" :style="{ color: pollutant.color }">
                    {{ pollutant.value.toFixed(1) }} µg/m³
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div class="hidden 2xl:block space-y-4 md:space-y-6">
            <div
              class="backdrop-blur-md bg-white/10 rounded-xl p-6 border border-white/20 shadow-2xl"
            >
              <h3 class="text-xl font-semibold mb-4">Air Quality Components</h3>
              <div class="h-64">
                <BarChart :data="pollutantData" />
              </div>
              <div class="grid grid-cols-2 md:grid-cols-3 gap-3 mt-4">
                <div
                  v-for="pollutant in pollutantData"
                  :key="pollutant.name"
                  class="backdrop-blur-md bg-white/10 rounded-lg p-3"
                >
                  <div class="text-sm opacity-80">{{ pollutant.name }}</div>
                  <div class="text-xl font-bold" :style="{ color: pollutant.color }">
                    {{ pollutant.value.toFixed(1) }} µg/m³
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'
import { Cloud, Wind, Droplets, Eye, Gauge, MapPin, Sun, Moon, Star, Trash2 } from 'lucide-vue-next'
import LineChart from './LineChart.vue'
import BarChart from './BarChart.vue'
import axios from 'axios'
import { setOptions, importLibrary } from '@googlemaps/js-api-loader'

const darkMode = ref(false)
const loading = ref(true)
const expandedView = ref(false)
const currentTime = ref(new Date())
const location = ref({ lat: 40.7128, lon: -74.006, name: 'New York' })
const weather = ref<any>(null)
const airQuality = ref<any>(null)
const forecast = ref<any[]>([])
const favorites = ref<any[]>([])
const mapContainer = ref<HTMLElement | null>(null)
const mapInstance = ref<google.maps.Map | null>(null)
const marker = ref<google.maps.Marker | null>(null)

// API Configuration
const WEATHER_API_KEY = import.meta.env.VITE_OPENWEATHER_API_KEY || 'YOUR_API_KEY'
const GOOGLE_MAPS_KEY = import.meta.env.VITE_GOOGLE_MAPS_KEY || 'YOUR_GOOGLE_MAPS_KEY'
let timeInterval: number

onMounted(async () => {
  loadFavorites()
  await initializeMap()
  getUserLocation()

  timeInterval = setInterval(() => {
    currentTime.value = new Date()
  }, 1000) as unknown as number
})

onUnmounted(() => {
  clearInterval(timeInterval)
})

watch(
  () => location.value,
  () => {
    fetchWeatherData()
    fetchAirQualityData()
    fetchForecastData()
    updateMapMarker()
  },
  { deep: true },
)

const bgClass = computed(() =>
  darkMode.value
    ? 'bg-gradient-to-br from-gray-900 via-blue-900 to-gray-900'
    : 'bg-gradient-to-br from-blue-400 via-cyan-400 to-blue-500',
)

const aqiLabel = computed(() => {
  if (!airQuality.value) return { label: 'N/A', color: 'text-gray-500' }

  const labels = ['Good', 'Fair', 'Moderate', 'Poor', 'Very Poor']
  const colors = [
    'text-green-500',
    'text-yellow-500',
    'text-orange-500',
    'text-red-500',
    'text-purple-500',
  ]
  const aqi = airQuality.value.aqi

  return {
    label: labels[aqi - 1] || 'Unknown',
    color: colors[aqi - 1] || 'text-gray-500',
  }
})

const pollutantData = computed(() => {
  if (!airQuality.value) return []

  return [
    { name: 'PM2.5', value: airQuality.value.components.pm2_5, color: '#3b82f6' },
    { name: 'PM10', value: airQuality.value.components.pm10, color: '#8b5cf6' },
    { name: 'O₃', value: airQuality.value.components.o3, color: '#06b6d4' },
    { name: 'NO₂', value: airQuality.value.components.no2, color: '#f59e0b' },
    { name: 'SO₂', value: airQuality.value.components.so2, color: '#ef4444' },
    { name: 'CO', value: airQuality.value.components.co / 10, color: '#10b981' },
  ]
})

// Initialize Google Maps
const initializeMap = async () => {
  if (!mapContainer.value) return

  setOptions({
    key: GOOGLE_MAPS_KEY,
    libraries: ['places'],
  })

  try {
    const { Map } = await importLibrary('maps')
    const { Marker } = await importLibrary('marker')
    mapInstance.value = new Map(mapContainer.value, {
      center: { lat: location.value.lat, lng: location.value.lon },
      zoom: 10,
      styles: darkMode.value ? getDarkMapStyle() : [],
      disableDefaultUI: false,
      zoomControl: true,
      mapTypeControl: false,
      streetViewControl: false,
      fullscreenControl: false,
    })

    marker.value = new Marker({
      position: { lat: location.value.lat, lng: location.value.lon },
      map: mapInstance.value,
      draggable: true,
    })
    // Click event on map
    mapInstance.value.addListener('click', async (e: any) => {
      if (e.latLng) {
        const lat = e.latLng.lat()
        const lon = e.latLng.lng()

        // Update marker position with animation
        if (marker.value) {
          marker.value.setPosition({ lat, lng: lon })
          marker.value.setAnimation(google.maps.Animation.BOUNCE)

          // Stop animation after 2 seconds
          setTimeout(() => {
            if (marker.value) {
              marker.value.setAnimation(null)
            }
          }, 700)
        }

        const locationName = await reverseGeocode(lat, lon)

        location.value = {
          lat,
          lon,
          name: locationName || 'Selected Location',
        }
      }
    })

    marker.value.addListener('dragend', async (e: any) => {
      if (e.latLng) {
        const lat = e.latLng.lat()
        const lon = e.latLng.lng()

        const locationName = await reverseGeocode(lat, lon)

        location.value = {
          lat,
          lon,
          name: locationName || 'Selected Location',
        }
      }
    })

    // Attach listeners etc...
  } catch (error) {
    console.error('Error loading Google Maps:', error)
  }
}

// Reverse geocode to get location name
const reverseGeocode = async (lat: number, lon: number): Promise<string> => {
  try {
    const geocoder = new google.maps.Geocoder()
    const response = await geocoder.geocode({
      location: { lat, lng: lon },
    })

    if (response.results[0]) {
      // Try to get city name
      const addressComponents = response.results[0].address_components
      const city = addressComponents.find((component) => component.types.includes('locality'))

      if (city) return city.long_name

      // Fallback to formatted address
      return response.results?.[0]?.formatted_address?.split(',')[0] ?? ''
    }
  } catch (error) {
    console.error('Reverse geocoding error:', error)
  }

  return 'Selected Location'
}

// Update map marker position
const updateMapMarker = () => {
  if (mapInstance.value && marker.value) {
    const newPos = { lat: location.value.lat, lng: location.value.lon }
    mapInstance.value.setCenter(newPos)
    marker.value.setPosition(newPos)
  }
}

// Dark mode map style
const getDarkMapStyle = () => [
  { elementType: 'geometry', stylers: [{ color: '#242f3e' }] },
  { elementType: 'labels.text.stroke', stylers: [{ color: '#242f3e' }] },
  { elementType: 'labels.text.fill', stylers: [{ color: '#746855' }] },
  {
    featureType: 'administrative.locality',
    elementType: 'labels.text.fill',
    stylers: [{ color: '#d59563' }],
  },
  {
    featureType: 'poi',
    elementType: 'labels.text.fill',
    stylers: [{ color: '#d59563' }],
  },
  {
    featureType: 'poi.park',
    elementType: 'geometry',
    stylers: [{ color: '#263c3f' }],
  },
  {
    featureType: 'poi.park',
    elementType: 'labels.text.fill',
    stylers: [{ color: '#6b9a76' }],
  },
  {
    featureType: 'road',
    elementType: 'geometry',
    stylers: [{ color: '#38414e' }],
  },
  {
    featureType: 'road',
    elementType: 'geometry.stroke',
    stylers: [{ color: '#212a37' }],
  },
  {
    featureType: 'road',
    elementType: 'labels.text.fill',
    stylers: [{ color: '#9ca5b3' }],
  },
  {
    featureType: 'road.highway',
    elementType: 'geometry',
    stylers: [{ color: '#746855' }],
  },
  {
    featureType: 'road.highway',
    elementType: 'geometry.stroke',
    stylers: [{ color: '#1f2835' }],
  },
  {
    featureType: 'road.highway',
    elementType: 'labels.text.fill',
    stylers: [{ color: '#f3d19c' }],
  },
  {
    featureType: 'transit',
    elementType: 'geometry',
    stylers: [{ color: '#2f3948' }],
  },
  {
    featureType: 'transit.station',
    elementType: 'labels.text.fill',
    stylers: [{ color: '#d59563' }],
  },
  {
    featureType: 'water',
    elementType: 'geometry',
    stylers: [{ color: '#17263c' }],
  },
  {
    featureType: 'water',
    elementType: 'labels.text.fill',
    stylers: [{ color: '#515c6d' }],
  },
  {
    featureType: 'water',
    elementType: 'labels.text.stroke',
    stylers: [{ color: '#17263c' }],
  },
]

const toggleDarkMode = () => {
  darkMode.value = !darkMode.value
  if (mapInstance.value) {
    mapInstance.value.setOptions({
      styles: darkMode.value ? getDarkMapStyle() : [],
    })
  }
}

const loadFavorites = () => {
  const saved = localStorage.getItem('climapure-favorites')
  if (saved) favorites.value = JSON.parse(saved)
}

const saveFavorites = (favs: any[]) => {
  favorites.value = favs
  localStorage.setItem('climapure-favorites', JSON.stringify(favs))
}

const getUserLocation = () => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      (position) => {
        location.value = {
          lat: position.coords.latitude,
          lon: position.coords.longitude,
          name: 'Current Location',
        }
      },
      (error) => {
        console.error('Geolocation error:', error)
        fetchWeatherData()
      },
    )
  } else {
    fetchWeatherData()
  }
}

const fetchWeatherData = async () => {
  loading.value = true
  try {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${location.value.lat}&lon=${location.value.lon}&units=metric&appid=${WEATHER_API_KEY}`,
    )

    weather.value = {
      temp: Math.round(response.data.main.temp),
      feels_like: Math.round(response.data.main.feels_like),
      humidity: response.data.main.humidity,
      pressure: response.data.main.pressure,
      wind_speed: response.data.wind.speed.toFixed(1),
      clouds: response.data.clouds.all,
      visibility: response.data.visibility,
      description: response.data.weather[0].description,
      icon: response.data.weather[0].icon,
    }

    if (location.value.name === 'Current Location' || location.value.name === 'Selected Location') {
      location.value.name = response.data.name
    }
  } catch (error) {
    console.error('Weather fetch error:', error)
    weather.value = {
      temp: 22,
      feels_like: 20,
      humidity: 65,
      pressure: 1013,
      wind_speed: 3.5,
      clouds: 40,
      visibility: 10000,
      description: 'Partly Cloudy',
      icon: '02d',
    }
  } finally {
    loading.value = false
  }
}

const fetchAirQualityData = async () => {
  try {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/air_pollution?lat=${location.value.lat}&lon=${location.value.lon}&appid=${WEATHER_API_KEY}`,
    )

    airQuality.value = {
      aqi: response.data.list[0].main.aqi,
      components: response.data.list[0].components,
    }
  } catch (error) {
    console.error('AQI fetch error:', error)
    airQuality.value = {
      aqi: 2,
      components: {
        pm2_5: 12.5,
        pm10: 18.2,
        o3: 45.3,
        no2: 25.1,
        so2: 8.5,
        co: 250,
      },
    }
  }
}

const fetchForecastData = async () => {
  try {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/forecast?lat=${location.value.lat}&lon=${location.value.lon}&units=metric&appid=${WEATHER_API_KEY}`,
    )

    forecast.value = response.data.list.slice(0, 8).map((item: any) => ({
      time: new Date(item.dt * 1000).toLocaleTimeString('en-US', { hour: 'numeric', hour12: true }),
      temp: Math.round(item.main.temp),
    }))
  } catch (error) {
    console.error('Forecast fetch error:', error)
    forecast.value = [
      { time: '12 AM', temp: 18 },
      { time: '3 AM', temp: 16 },
      { time: '6 AM', temp: 15 },
      { time: '9 AM', temp: 19 },
      { time: '12 PM', temp: 22 },
      { time: '3 PM', temp: 24 },
      { time: '6 PM', temp: 21 },
      { time: '9 PM', temp: 19 },
    ]
  }
}

const addToFavorites = () => {
  if (!favorites.value.some((f) => f.lat === location.value.lat && f.lon === location.value.lon)) {
    saveFavorites([...favorites.value, { ...location.value }])
  }
}

const removeFavorite = (index: number) => {
  const updated = favorites.value.filter((_, i) => i !== index)
  saveFavorites(updated)
}

const selectFavorite = (fav: any) => {
  location.value = { ...fav }
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition:
    opacity 0.3s ease,
    transform 0.3s ease;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>
