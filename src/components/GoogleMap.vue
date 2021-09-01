<template>
  <div class="container">
    <button
      class="track-btn"
      v-on:click="handleTracking"
      v-text="tracking ? 'Cancel Tracking' : 'Start Tracking'"
    ></button>
    <button
      class="track-btn"
      v-on:click="driverSwitch"
      v-text="'Simulate driver switch'"
    ></button>
    <button
      class="track-btn warn"
      v-on:click="resetMarker"
      v-text="'Reset'"
    ></button>
    <p v-text="status"></p>
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
        :position="
          google &&
          new google.maps.LatLng(currentPosition[0], currentPosition[1])
        "
        :icon="markerImage"
      ></gmap-marker>
      <!-- <DirectionsRenderer
        travelMode="DRIVING"
        :origin="
          positions && new google.maps.LatLng(positions[0][0], positions[0][1])
        "
        :destination="
          positions &&
          new google.maps.LatLng(
            positions[positions.length - 1][0],
            positions[positions.length - 1][1]
          )
        "
      /> -->
    </gmap-map>
  </div>
</template>
<script>
import { Vue } from "vue-property-decorator";
import * as VueGoogleMaps from "vue2-google-maps";
import DirectionsRenderer from "@/components/DirectionsRenderer";
import { formatDistance } from "date-fns";
Vue.use(VueGoogleMaps, {
  load: {
    key: process.env.API_KEY,
    libraries: "places", // This is required if you use the Autocomplete plugin
  },
});
export default {
  components: { DirectionsRenderer },
  computed: {
    google: VueGoogleMaps.gmapApi,
    // waypoints:function(){
    //   return [[-1.297459, 36.776747]]
    // }
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
      currentAddress: "",
      lastUpdatedTime: null,
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
      tracking: false,
      status:''
    };
  },

  mounted() {
    this.currentPosition = this.centerCoods;
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
    this.$refs.mapRef.$mapPromise.then(() => {
      this.mapInitialized = true;
      this.getAddress();
      this.lastUpdatedTime = Date.now();
    });
    this.markerImage = require("../assets/25_freight.png");
  },
  methods: {
    handleTracking() {
      this.centerCoods = this.currentPosition = [-1.298982, 36.776811];
      clearInterval(this.interval);
      if(this.tracking){
        this.tracking = !this.tracking;
      }else{
        this.pollCoordinates();
      }
    },

    resetMarker(){
      this.driverDetails.vehicleInfo = 'KAY 747E';
      this.centerCoods = this.currentPosition = [-1.298982, 36.776811];
      clearInterval(this.interval);
      this.tracking = false;
    },

    pollCoordinates() {
      this.tracking = true;
      var count = 1;

      this.interval = setInterval(() => {
        this.animateMarker(this.positions[count], this.currentPosition);
        this.infoContent = this.getInfoWindowContent();
        this.lastUpdatedTime = Date.now();
        if (count == this.positions.length - 1) {
          this.tracking = false;
          clearInterval(this.interval);
        } else {
          count++;
        }
      }, 5000);
    },
    animateMarker(endPosition, startPosition, count = 0) {
      let steps = 600;
      let miniDelay = 5;
      let latDelta = (endPosition[0] - startPosition[0]) / steps;
      let lngDelta = (endPosition[1] - startPosition[1]) / steps;

      for (var i = 0; i < steps; i++) {
        (() => {
          let intermediateLat = startPosition[0] + i * latDelta;
          let intermediateLng = startPosition[1] + i * lngDelta;
          setTimeout(() => {
            this.centerCoods = this.currentPosition = [
              intermediateLat,
              intermediateLng,
            ];
          }, miniDelay);
        })();
      }
    },
    getInfoWindowContent() {
      this.getAddress();
      const timeDiff = formatDistance(this.lastUpdatedTime, new Date(), {
        addSuffix: true,
      });
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
          <div class="location-updated">
            <span>Location updated </span><span> ${timeDiff}</span>
          </div>
        </div>
      `;
    },
    getAddress() {
      if (this.mapInitialized) {
        const geocoder = new google.maps.Geocoder();
        geocoder
          .geocode({
            location: {
              lat: this.currentPosition[0],
              lng: this.currentPosition[1],
            },
          })
          .then((response) => {
            if (response.results[0]) {
              this.currentAddress = response.results[0].formatted_address;
              this.infoContent = this.getInfoWindowContent();
              console.log(this.currentAddress);
            }
          });
      }
    },
    driverSwitch(){
      console.log("foo");
      // Position driver A at the desired location
      const newDriverALocation = [-1.300355, 36.773850];
      const driverBLocation = [-1.291879, 36.778389];
      this.status = "Switching driver A to new position";
      this.animateMarker(newDriverALocation,this.currentPosition);
      this.infoContent = this.getInfoWindowContent();
      this.lastUpdatedTime = Date.now();

      setTimeout(()=>{
        this.status  = "Transitioning to driver B";
        this.driverDetails.vehicleInfo = "KAD 432E";
        this.animateMarker(driverBLocation,this.currentPosition);
        this.infoContent = this.getInfoWindowContent();
        this.lastUpdatedTime = Date.now();

        this.status("Done");
      },5000);
    }
  },
};
</script>
<style  lang="scss">
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
  margin-right: 10px;
}

.track-btn.warn{
  background:orangered;
}
.vehicle-info {
  font-weight: 500;
}
.vehicle-details > span {
  border-right: 1px solid grey;
  padding-right: 20px;
  padding-left: 3px;
}
.location-updated > span {
  color: #00000066;
}
</style>