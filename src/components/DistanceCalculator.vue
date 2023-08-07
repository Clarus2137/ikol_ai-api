<template>
   <div class="container">
      <div class="row">
         <div class="clmn">
            <DistanceForm @calculate="calculateDistance" @reset="resetResult" />
         </div>

         <div v-if="result" class="clmn">
            <div class="result">
               <p class="h1" align="center">Odległość podanych punktów wynosi:</p>
               <p>Metrów: {{ result.meters }}</p>
               <p>Kilometrów: {{ result.kilometers }}</p>
            </div>
         </div>
      </div>
   </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import axios from 'axios';
import DistanceForm from './DistanceForm.vue'

interface DistanceResult {
   meters: number;
   kilometers: number;
}

export default defineComponent({
   name: 'DistanceCalculator',
   components: {
      DistanceForm
   },
   data() {
      return {
         result: null as DistanceResult | null,
      };
   },
   methods: {
      async calculateDistance(origin: string, destination: string) {
         try {
            const response = await axios.get('https://api.distancematrix.ai/maps/api/distancematrix/json', {
               params: {
                  units: 'metric',
                  origins: origin,
                  destinations: destination,
                  key: 'UKltbYbwCIymsj65Sqe38CC7gpsAh' // API-key
               }
            });

            const distanceData = response.data.rows[0].elements[0].distance;
            this.result = {
               meters: distanceData.value,
               kilometers: distanceData.value / 1000
            };
         } catch (error) {
            console.error('Błąd przy obliczaniu.', error);
         }
      },
      
      resetResult() {
         this.result = null;
      },
   },
});
</script>

<style scope lang="scss">
.result {
   text-align: center;
   color: #fff;

   &>p {
      margin-bottom: 15px;
   }
}
</style>
