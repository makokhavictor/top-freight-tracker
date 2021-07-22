<template>
  <div>
    <button class="track-btn" v-on:click="startTracking">Start tracking</button>
    <GmapMap
      :center="{ lat: centerCoods[0], lng: centerCoods[1] }"
      :zoom="18"
      map-type-id="roadmap"
      style="width: 100%; height: 600px"
    >
      <GmapMarker
        ref="myMarker"
        :position="
          google &&
          new google.maps.LatLng(currentPosition[0], currentPosition[1])
        "
        :icon="markerImage"
      />
    </GmapMap>
  </div>
</template>
<script>
import { Vue } from "vue-property-decorator";
import * as VueGoogleMaps from "vue2-google-maps";
const API_KEY = "AIzaSyB9LGGKDb13mSgC1X-m9h0ZnVUlM8STR8A";
Vue.use(VueGoogleMaps, {
  load: {
    key: API_KEY,
    libraries: "places", // This is required if you use the Autocomplete plugin
  },
});
export default {
  computed: {
    google: VueGoogleMaps.gmapApi,
  },
  data() {
    return {
      centerCoods: [-1.298982, 36.776811],
      currentPosition: [],
      positions: null,
      markerImage: null,
      interval:null
    };
  },
  mounted() {
    this.markerImage = require("../assets/25_freight.png");
    this.currentPosition = this.centerCoods;
  },
  methods: {
    startTracking(){
      this.centerCoods = [-1.298982, 36.776811];
      this.currentPosition = this.centerCoods;
      clearInterval(this.interval);
      this.pollCoordinates();
    },
    pollCoordinates() {
      this.positions = [
        [-1.298982, 36.776811],
        [-1.297459, 36.776747],
        [-1.296193, 36.776726],
        [-1.296097, 36.779236],
        [-1.296151, 36.777637],
        [-1.296215, 36.776693],
        [-1.294252, 36.776586],
        [-1.294048, 36.77679],
        [-1.293973, 36.779118],
        [-1.292622, 36.779075],
        [-1.291844, 36.779049],
      ];

      var count = 0;
      var intermediateGap = 800;
      var intermediateLat;
      var intermediateLng;
      this.interval = setInterval(() => {
        intermediateLat =
          (this.positions[count][0] - this.currentPosition[0]) /
          intermediateGap;
        intermediateLng =
          (this.positions[count][1] - this.currentPosition[1]) /
          intermediateGap;
        this.currentPosition = [
          this.positions[count][0] + intermediateLat,
          this.positions[count][1] + intermediateLng,
        ];
        this.centerCoods = this.currentPosition;

        if (count == this.positions.length - 1) {
          clearInterval(this.interval);
        } else {
          count++;
        }
      }, 2000);
    },
  },
};
</script>
<style scoped lang="scss">
.track-btn{
      outline: none;
    border: none;
    background: #3d6ca8;
    color: #fff;
    height: 40px;
    width: max-content;
    padding: 10px 20px;
    border-radius: 5px;
    margin-bottom: 1rem;
}
</style>