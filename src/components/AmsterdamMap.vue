<template>
  <div class="amsterdam-map">
    <l-map
      ref="map"
      class="inner-map"
      :zoom="zoom"
      :center="[lat, lng]"
      :max-bounds="maxBounds"
      :max-zoom="16"
      :min-zoom="3"
      @update:center="didMove"
      :crs="crs"
      :options="mapOptions"
    >
      <l-tile-layer
        :url="url"
        :tms="true"
        :detect-retina="true"
        subdomains="1234"
        attribution="Amsterdam Maps"
      />
      <l-marker
        v-for="(marker, m) in markers"
        :key="'marker-' + m"
        :lat-lng="[marker.lat, marker.lng]"
        :icon="icon"
      >
        <div class="headline">
          Test
        </div>
      </l-marker>
    </l-map>
  </div>
</template>

<script>
import { LMap, LTileLayer, LMarker } from "vue2-leaflet"
import L, { icon } from "leaflet"
import proj4 from "proj4"
import VueTypes from "vue-types"

export default {
  name: "AmsterdamMap",
  components: {
    LMap,
    LTileLayer,
    LMarker,
  },
  props: {
    lat: VueTypes.number,
    lng: VueTypes.number,
    markers: VueTypes.Array
  },

  data() {
    return {
      url: "https://t{s}.data.amsterdam.nl/topo_rd/{z}/{x}/{y}.png",
      mapOptions: {
        zoomSnap: 0.5,
      },
      zoom: 14,
      crs: this.getCrsRd(),
      maxBounds: [
        [52.25168, 4.64034],
        [52.50536, 5.10737],
      ],
      icon: icon({
        iconUrl: require("@/assets/marker.png"),
        iconSize: [20, 30],
        iconAnchor: [0, 30],
      }),
    }
  },

  methods: {
    didMove (event) {
      this.$emit('update:lat', event.lat)
      this.$emit('update:lng', event.lng)
    },

    getCrsRd(maxZoom = 16, zeroScale = 3440.64, scales = []) {
      for (let i = 0; i <= maxZoom; i++) {
        scales.push(1 / (zeroScale * 0.5 ** i))
      }

      const projDefinition = `+proj=sterea +lat_0=52.15616055555555 +lon_0=5.38763888888889 +k=0.9999079 +x_0=155000 +y_0=463000 +ellps=bessel +towgs84=565.4171,50.3319,465.5524,-0.398957,0.343988,-1.87740,4.0725 +units=m +no_defs'`
      const proj4RD = proj4("WGS84", projDefinition)

      return {
        ...L.CRS.Simple,
        ...{
          code: "EPSG:28992",
          infinite: false,
          projection: {
            project: (latlng) => {
              const point = proj4RD.forward([latlng.lng, latlng.lat])
              return new L.Point(point[0], point[1])
            },
            unproject: (point) => {
              const lnglat = proj4RD.inverse([point.x, point.y])
              return L.latLng(lnglat[1], lnglat[0])
            },

            bounds: L.bounds([-285401.92, 903401.92], [595401.92, 22598.08]),
          },
          transformation: new L.Transformation(1, 285401.92, -1, 903401.92),

          scale: (zoom) => {
            if (scales[zoom]) {
              return scales[zoom]
            }
            return 1 / (zeroScale * 0.5 ** zoom)
          },

          zoom: (scale) => Math.log(1 / scale / zeroScale) / Math.log(0.5),
        },
      }
    },
  },
}
</script>

<style scoped>
.amsterdam-map {
  height: 500px;
  max-height: 80%;
  max-width: 80%;
  margin: auto;
}

.inner-map {
  z-index: 10;
  /* padding: 1rem; */
  margin: 1rem;
}
</style>