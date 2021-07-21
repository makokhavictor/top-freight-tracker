<template>
  <v-container> <div ref="map" class="map-cont"></div> </v-container>
</template>
<script lang="ts">
import { Vue, Component } from 'vue-property-decorator';
import { Loader } from '@googlemaps/js-api-loader';
@Component({})
export default class Map extends Vue {
  public loader: any;
  public mapOptions: any = {};
  public mounted() {
    console.log('Map created');
    this.mapOptions = {
      center: {
        lat: -1.298982,
        lng: 36.776811,
      },
      zoom: 4,
    };
    this.loader = new Loader({
      apiKey: 'AIzaSyB9LGGKDb13mSgC1X-m9h0ZnVUlM8STR8A',
      libraries: ['places'],
    });

    this.loader
      .load()
      .then((google: any) => {
        new google.maps.Map(this.$refs.map, this.mapOptions);
      })
      .catch((e: any) => {
        // do something
      });
  }
}
</script>
<style lang="scss" scoped>
    .map-cont{
        width:100%;
        height: 500px;
    }
</style>