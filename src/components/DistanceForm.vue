<template>
   <p class="h2" align="center">By obliczyć odległość dla swojej trasy, skorzystaj z poniższego formularza. Wprowadź dane w formacie "szerokość geograficzna, długość geograficzna" zarówno dla pierwszego punktu, jak i dla drugiego. Na przykład: 52.207441370644965, 20.91503603732403.</p>

   <form class="form" @submit.prevent="submitForm">
      <div class="form__coordinates">
         <input v-model="origin" type="text" class="form__input" placeholder="Współrzędne początkowe" required>
         <p v-if="originError" class="form__error">Sprawdź poprawność wprowadzonych danych</p>
         <p v-if="showCoordinatesAlert" class="form__error">Wprowadź różne pary współrzędnych</p>
      </div>
      <div class="form__coordinates">
         <input v-model="destination" type="text" class="form__input" placeholder="Współrzędne końcowe" required>
         <p v-if="destinationError" class="form__error">Sprawdź poprawność wprowadzonych danych</p>
         <p v-if="showCoordinatesAlert" class="form__error">Wprowadź różne pary współrzędnych</p>
      </div>
      <div class="form__submit">
         <button type="submit" class="form__btn btn">Oblicz</button>
         <button type="button" class="form__btn btn" @click="resetForm">Resetuj</button>
      </div>
   </form>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
   name: 'DistanceForm',
   data() {
      return {
         origin: '',
         destination: '',
         originError: false,
         destinationError: false,
         showCoordinatesAlert: false,
      };
   },
   methods: {
      submitForm() {
         if (
            this.isValidCoordinates(this.origin) &&
            this.isValidCoordinates(this.destination) &&
            this.areCoordinatesDifferent()
         ) {
            this.originError = false;
            this.destinationError = false;
            this.showCoordinatesAlert = false; // Скрыть алерт при успешной валидации
            this.$emit('calculate', this.origin, this.destination);
         } else {
            this.originError = !this.isValidCoordinates(this.origin);
            this.destinationError = !this.isValidCoordinates(this.destination);

            if (!this.areCoordinatesDifferent()) {
               this.showCoordinatesAlert = true;
            } else {
               this.showCoordinatesAlert = false;
            }
         }
      },

      isValidCoordinates(coordinate: string): boolean {
         const coordinatePattern = /^-?\d+(\.\d+)?,\s*-?\d+(\.\d+)?$/;
         return coordinatePattern.test(coordinate.trim());
      },

      areCoordinatesDifferent(): boolean {
         return this.origin !== this.destination;
      },

      resetForm() {
         this.origin = '';
         this.destination = '';
         this.originError = false;
         this.destinationError = false;
         this.$emit('reset');
      },
   },
   emits: ['calculate', 'reset'],
});
</script>

<style scope lang="scss">
.h2 {
   margin-bottom: 30px;
}

.form {
   width: 75%;
   max-width: 500px;
   min-width: 290px;
   margin: 0 auto;
   text-align: center;

   & .form__coordinates {
      margin-bottom: 30px;
   }

   & .form__input {
      width: 100%;
      border: none;
      border-bottom: 2px solid #000;
      background: transparent;
      text-align: center;
      color: #fff;
      text-shadow: 0 0 5px #000;

      &:focus {
         outline: none;
      }
   }

   & .form__error {
      color: #f00;
      text-shadow: 0 0 rgba(255, 255, 255, .7);
   }

   & .form__btn:not(:last-child) {
      margin-right: 30px;
   }
}
</style>
