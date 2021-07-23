<template>
  <div>
    <button class="track-btn" v-on:click="startTracking">Start tracking</button>
    <gmap-map
      ref="mapRef"
      :center="{ lat: centerCoods[0], lng: centerCoods[1] }"
      :zoom="18"
      map-type-id="roadmap"
      style="width: 100%; height: 600px"
    >
      <gmap-info-window
        :options="infoOptions"
        :position="{ lat: currentPosition[0], lng: currentPosition[1] }"
        :opened="infoWinOpen"
        @closeclick="infoWinOpen = false"
      >
        <div v-html="infoContent"></div>
      </gmap-info-window>
      <gmap-marker
        ref="myMarker"
        :position="
          google &&
          new google.maps.LatLng(currentPosition[0], currentPosition[1])
        "
        :icon="markerImage"
      ></gmap-marker>
      <DirectionsRenderer 
        travelMode="DRIVING" 
        :origin="positions && new google.maps.LatLng(positions[0][0], positions[0][1])" 
        :destination="positions && new google.maps.LatLng(positions[positions.length-1][0], positions[positions.length-1][1])"/>
    </gmap-map>
  </div>
</template>
<script>
import { Vue } from "vue-property-decorator";
import * as VueGoogleMaps from "vue2-google-maps";
import DirectionsRenderer from "@/components/DirectionsRenderer";
const API_KEY = "AIzaSyB9LGGKDb13mSgC1X-m9h0ZnVUlM8STR8A";
// const API_KEY = "AIzaSyCedNpxsrE_L8ELr6c7wNRZbYR2FMBe03Q";
Vue.use(VueGoogleMaps, {
  load: {
    key: API_KEY,
    libraries: "places", // This is required if you use the Autocomplete plugin
  },
});
export default {
  components: {DirectionsRenderer},
  computed: {
    google: VueGoogleMaps.gmapApi,
  },
  data() {
    return {
      centerCoods: [-1.298982, 36.776811],
      currentPosition: [],
      positions: null,
      markerImage: null,
      interval: null,
      infoWindowPos: null,
      infoWinOpen: true,
      infoContent: "",
      mapInitialized: false,
      currentAddress:"",
      driverDetails: {
        vehicleInfo: "KAY 747E",
        vehicleSize: "27 tonnes",
        cargoType: "Flatbed",
        goodsType: "Rice",
      },
      infoOptions: {
        pixelOffset: {
          width: 0,
          height: -35,
        },
      },
    };
  },

  
  mounted() {
    this.currentPosition = this.centerCoods;
    this.$refs.mapRef.$mapPromise.then(() => {
      this.mapInitialized = true;
      this.getAddress();
    });
    this.markerImage = require("../assets/25_freight.png");
    
    
  },
  methods: {
    startTracking() {
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
      var intermediateGap = 500;
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
        this.infoContent = this.getInfoWindowContent();

        if (count == this.positions.length - 1) {
          clearInterval(this.interval);
        } else {
          count++;
        }
      }, 2000);
    },
     getInfoWindowContent() {
       this.getAddress();
        return `
        <div class="markerInfo">
          <div class="vehicle-info">${this.driverDetails.vehicleInfo}</div>
          <div class="vehicle-details">
            <span>${this.driverDetails.vehicleSize}</span>
            <span>${this.driverDetails.cargoType}</span>
            <span>${this.driverDetails.goodsType}</span>
          </div>
          <div class="current-location">
          <span>Location:</span>
          <span>${this.currentAddress}</span>
          </div>
        </div>
      `;
    },
    getAddress(){
      if(this.mapInitialized){
        const geocoder = new google.maps.Geocoder();
        geocoder.geocode({location:{lat:this.currentPosition[0],lng:this.currentPosition[1]}})
        .then((response)=>{
          if (response.results[0]) {
            this.currentAddress = response.results[0].formatted_address;
            this.infoContent = this.getInfoWindowContent();
            console.log(this.currentAddress);
          }
        });

      }
    }
  },
};
</script>
<style scoped lang="scss">
.track-btn {
  outline: none;
  border: none;
  background: #3d6ca8;
  color: #fff;
  height: 40px;
  width: max-content;
  padding: 10px 20px;
  border-radius: 5px;
  margin-bottom: 1rem;
  cursor: pointer;
}
.vehicle-info {
  font-weight: 500;
}
.vehicle-details > span {
  border-right: 1px solid grey;
  padding-right: 3px;
}
</style>