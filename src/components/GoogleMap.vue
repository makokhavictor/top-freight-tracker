<template>
  <GmapMap
    :center="{ lat:centerCoods[0], lng: centerCoods[1] }"
    :zoom="16"
    map-type-id="roadmap"
    style="width: 100%; height: 520px"
  >
    <GmapMarker
      :position="google && new google.maps.LatLng(centerCoods[0], centerCoods[1])"
      :draggable="true"
    />
  </GmapMap>
</template>
<script>
import { Vue } from "vue-property-decorator";
import * as VueGoogleMaps from "vue2-google-maps";
Vue.use(VueGoogleMaps, {
  load: {
    key: "AIzaSyB9LGGKDb13mSgC1X-m9h0ZnVUlM8STR8A",
    libraries: "places", // This is required if you use the Autocomplete plugin
  },
});
export default {
  computed: {
    google: VueGoogleMaps.gmapApi,
  },
  data() {
    return {
        centerCoods:[-1.298982,36.776811],
    };
  },
  mounted() {},
};
</script>
<style scoped>
.gmap {
  width: 100%;
  height: 500px;
}
</style>