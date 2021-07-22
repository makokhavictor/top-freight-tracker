<template>
  <GmapMap
    :center="{ lat: centerCoods[0], lng: centerCoods[1] }"
    :zoom="16"
    map-type-id="roadmap"
    style="width: 100%; height: 520px"
  >
    <GmapMarker
      ref="myMarker"
      :position="
        google && new google.maps.LatLng(currentPosition[0], currentPosition[1])
      "
      :draggable="true"
    />
  </GmapMap>
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
      positions: [],
    };
  },
  mounted() {
    this.currentPosition = this.centerCoods;
    this.pollCoordinates();

    // setTimeout(() => {
    //   this.currentPosition = [-1.297459, 36.776747];
    // }, 3000);
  },
  methods: {
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
        [-1.291844, 36.779049]
      ];

      var count = 0;

      setInterval(()=>{
        this.currentPosition = this.positions[count];
        this.centerCoods = this.currentPosition;
        count++;
        if(count > this.positions.length){
          clearInterval();
        }
      },2000);
    },
  },
};
</script>
<style scoped>
.gmap {
  width: 100%;
  height: 500px;
}
</style>