<template>
  <div id="app">
    <h4>Amsterdam Maps Example</h4>
    <div>
      <label for="lat">
        Lat
        <input
          name="Lat"
          v-model="lat"
        />
      </label>
      <label for="lng">
        Lng
        <input
          name="lng"
          v-model="lng"
        />
      </label>
    </div>
    <AmsterdamMap
      :lat.sync="lat"
      :lng.sync="lng"
      :markers="markers"
    />
    <footer>
      <span class="caption">Mani Gudvardarson 2021</span>
    </footer>
  </div>
</template>

<script>
import AmsterdamMap from './components/AmsterdamMap'

export default {
  name: 'App',

  components: {
    AmsterdamMap
  },

  methods: {
    didMove (event) {
      this.lat = event.lat
      this.lng = event.lng
    }
  },

  data () {
    return {
      lat: 52.3676876,
      lng: 4.8980516,
      markers: [
        {
          lat: 52.3676876,
          lng: 4.8980516
        }
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

footer {
  position: absolute;
  bottom: 0;
}

footer > .caption {
  font-size: 12px;
}
</style>
