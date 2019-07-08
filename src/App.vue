<template>
  <div id="app">
    <l-map
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      @update:zoom="zoomUpdated"
      @update:center="centerUpdated"
      @update:bounds="boundsUpdated"
    >
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker :lat-lng="marker"></l-marker>
      <l-choropleth-layer
        :data="pyDepartmentsData"
        titleKey="department_name"
        idKey="department_id"
        :value="value"
        :extraValues="extraValues"
        geojsonIdKey="dpto"
        :geojson="paraguayGeojson"
        :colorScale="colorScale"
      >
        <template slot-scope="props">
          <l-info-control
            :item="props.currentItem"
            :unit="props.unit"
            title="Department"
            placeholder="Hover over a department"
          />
          <l-reference-chart
            title="Girls school enrolment"
            :colorScale="colorScale"
            :min="props.min"
            :max="props.max"
            position="topright"
          />
        </template>
      </l-choropleth-layer>
    </l-map>
  </div>
</template>

<script>
import { InfoControl, ReferenceChart, ChoroplethLayer } from "vue-choropleth";
import { geojson } from "./data/py-departments-data";
import paraguayGeojson from "./data/paraguay.json";
import { pyDepartmentsData } from "./data/py-departments-data";
import { LMap, LTileLayer, LMarker } from "vue2-leaflet";
export default {
  name: "app",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    "l-info-control": InfoControl,
    "l-reference-chart": ReferenceChart,
    "l-choropleth-layer": ChoroplethLayer
  },
  data() {
    return {
      pyDepartmentsData,
      paraguayGeojson,
      colorScale: ["e7d090", "e9ae7b", "de7062"],
      value: {
        key: "amount_w",
        metric: "% girls"
      },
      extraValues: [
        {
          key: "amount_m",
          metric: "% boys"
        }
      ],
      mapOptions: {
        attributionControl: false
      },
      currentStrokeColor: "3d3213",
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
