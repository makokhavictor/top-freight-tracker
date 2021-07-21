<template>
  <v-container>
     <Map></Map>
    </v-container>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Map from '@/components/Map.vue';

@Component({
  components: {
    Map,
  },
})
export default class Home extends Vue {}
</script>
