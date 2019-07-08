<template>
  <div id="app">
    <!-- <img src="./assets/logo.png" />
    <h1>{{ msg }}</h1>-->
    <l-map
      :zoom="zoom"
      :center="center"
      @update:zoom="zoomUpdated"
      @update:center="centerUpdated"
      @update:bounds="boundsUpdated"
    >
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker :lat-lng="marker"></l-marker>
    </l-map>
  </div>
</template>

<script>
import { LMap, LTileLayer, LMarker, LControlZoom } from "vue2-leaflet";

export default {
  name: "app",
  components: { LMap, LTileLayer, LMarker, LControlZoom },
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      zoomControl: false,
      attributionControl: false,
      minZoom: 2,
      maxZoom: 7,
      zoom: 5, // From 0-18

      center: L.latLng(47.41322, -1.219482),
      url: "http://{s}.tile.osm.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      marker: L.latLng(47.41322, -1.219482)
    };
  },
  methods: {
    zoomUpdated(zoom) {
      if (zoom < this.minZoom || zoom > this.maxZoom) return;
      this.zoom = zoom;
      console.info("Zoom to: ", zoom);
    },
    centerUpdated(center) {
      this.center = center;
    },
    boundsUpdated(bounds) {
      this.bounds = bounds;
    }
  }
};

// create a red polygon from an array of LatLng points
var latlngs = [[37, -109.05], [41, -109.03], [41, -102.05], [37, -102.04]];
var polygon = L.polygon(latlngs, { color: "red" }).addTo(LMap);
// zoom the map to the polygon
LMap.fitBounds(polygon.getBounds());
</script>

<style lang="scss">
html,
body,
#app {
  height: 100%;
  margin: 0;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  // text-align: center;
  color: #2c3e50;
  // margin-top: 60px;
}

h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
