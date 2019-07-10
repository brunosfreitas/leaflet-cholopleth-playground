<template>
  <div id="app">
    <l-map
      v-bind="l_map_props"
      @update:zoom="zoomUpdated"
      @update:center="centerUpdated"
      @update:bounds="boundsUpdated"
    >
      <!-- TODO zoomout overlay not resizing -->
      <!-- TODO Search for l-map and l-tile-layer not sync -->
      <l-tile-layer v-bind="l_tile_layer_props"></l-tile-layer>
      <l-marker v-for="a_marker in l_marker_props" :key="a_marker.ley" :lat-lng="a_marker.value"></l-marker>
      <l-choropleth-layer
        v-bind="l_choropleth_layer_props"
        titleKey="department_name"
        idKey="id"
        geojsonIdKey="id"
        :zoom="l_map_props.zoom"
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
            v-bind="l_reference_chart_props"
            :min="props.min"
            :max="props.max"
          />
        </template>
      </l-choropleth-layer>
    </l-map>
  </div>
</template>

<script>
// As in: https://leafletjs.com/examples/choropleth/
// and: https://github.com/voluntadpear/vue-choropleth
import { InfoControl, ReferenceChart, ChoroplethLayer } from "vue-choropleth";
// import { geojson } from "./data/py-departments-data";

// Data get at: https://github.com/johan/world.geo.json/blob/master/countries.geo.json?short_path=afdfc39
// If you need to import map data from a external server: https://korigan.github.io/Vue2Leaflet/#/components/l-geo-json/
import countriesGeojson from "./data/countries.json";
// import { pyDepartmentsData } from "./data/py-departments-data";
import { countriesData } from "./data/countries-data";
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
      /*
       ** Props for LMap
       */
      l_map_props: {
        // Initial zoom -- From 0-18
        zoom: 5,

        // Jump to the world where the layers exists.
        // TODO Solve a way to only render the map area, without copies - mapBounds
        worldCopyJump: true,

        options: {
          // attributionControl: false,
          minZoom: 3,
          maxZoom: 7
        },

        // Where the map starts - could be the user location
        center: L.latLng(47.41322, -1.219482)
      },

      // A marker
      l_marker_props: {
        marker: {
          key: 0,
          value: L.latLng(47.41322, -1.219482)
        },
        marker2: {
          key: 1,
          value: L.latLng(50.41322, -3.219482)
        }
      },

      /*
       ** Props for l-choropleth-layer
       */
      l_choropleth_layer_props: {
        // The polygonal paths for countries
        geojson: countriesGeojson,
        // The data for each country
        data: countriesData,

        // Remove the white stroke of unselected countries
        strokeWidth: 0,
        // Selected/Mouseover country option
        currentStrokeWidth: 2,
        currentStrokeColor: "ffdd00",
        value: {
          key: "amount_w",
          metric: "% girls"
        },
        extraValues: [
          {
            key: "amount_m",
            metric: "% boys"
          }
        ]
      },

      /*
       ** Props for LTileLayer
       */
      l_tile_layer_props: {
        // -- Colored map with sea borders, local language based names --
        // url: "http://{s}.tile.osm.org/{z}/{x}/{y}.png",

        // -- Grayscale map, english
        url:
          "https://cartodb-basemaps-{s}.global.ssl.fastly.net/light_all/{z}/{x}/{y}.png",
        attribution:
          '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors, &copy; <a href="https://carto.com/attribution" alt="Map tiles by Carto, under CC BY 3.0. Data by OpenStreetMap, under ODbL">Carto</a>'
      },

      /*
       ** Props for l-reference-chart
       */
      l_reference_chart_props: {
        // Good acessibility map colours http://colorbrewer2.org/#type=diverging&scheme=RdYlGn&n=5
        colorScale: ["d7191c", "fdae61", "ffffbf", "a6d96a", "1a9641"],
        position: "topright"
      }
    };
  },
  methods: {
    zoomUpdated(zoom) {
      this.l_map_props.zoom = zoom;
      console.info("Zoom to: ", zoom);
    },
    centerUpdated(center) {
      this.l_map_props.center = center;
    },
    boundsUpdated(bounds) {
      this.l_map_props.bounds = bounds;
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
