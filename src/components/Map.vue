<template>
  <v-container> <div ref="map" class="map-cont"></div> </v-container>
</template>
<script lang="ts">
import { Vue, Component } from "vue-property-decorator";
import { Loader } from "@googlemaps/js-api-loader";
declare var google: any;

@Component({})
export default class Map extends Vue {
  public loader: any;
  public mapOptions: any = {};
  public marker: any;
  public initialPosition:any;
  public initialLatLng:any;
  public markerIcon:string = '';


  public mounted() {
    console.log("Map created");
    this.initialPosition = [ -1.298982,36.776811];
    this.initialLatLng = new google.maps.LatLng(this.initialPosition[0], this.initialPosition[1]);
    this.markerIcon = '@/assets/25_freight.png';

    this.mapOptions = {
      center: this.initialLatLng,
      mapTypeId: google.maps.MapTypeId.ROADMAP,
      zoom: 16,
    };

    this.loader = new Loader({
      apiKey: "AIzaSyB9LGGKDb13mSgC1X-m9h0ZnVUlM8STR8A",
      libraries: ["places"],
    });

    this.loader
      .load()
      .then((google: any) => {
        let map = new google.maps.Map(this.$refs.map, this.mapOptions);
        this.marker = new google.maps.Marker({
          position: this.initialLatLng,
          map: map,
          title: "Latitude:" + this.initialPosition[0] + " | Longitude:" + this.initialPosition[1],
          icon:this.markerIcon
        });
      })
      .catch((e: any) => {
        // do something
      });
  }
}
</script>
<style lang="scss" scoped>
.map-cont {
  width: 100%;
  height: 500px;
}
</style>