<template>
    <v-card
    :loading="loading"
    class="mx-auto my-8 bgCard"
    max-width="1300"
    elevation="16"
  >
  <template v-slot:loader="{ isActive }">
      <v-progress-linear
        :active="isActive"
        color="red-lighten-1"
        height="4"
        indeterminate
      ></v-progress-linear>
    </template>

    <v-card-item>
      <v-card-title class="titleCard">
        {{ Title }}
      </v-card-title>
      <v-card-subtitle>
        <FormDl @send-data="handleReceivedData"/>
      </v-card-subtitle>
    </v-card-item>

  </v-card>
</template>
<script setup lang="ts">
import { ref } from 'vue';

const props = defineProps({
  Title: {
    type: String,
    required: true
  },
  loading: {
    type: Boolean,
    required: false
  },
});
const loading = ref(props.loading || false);

function handleReceivedData(data: boolean) {
      // Traiter les données reçues du composant enfant
      setTimeout(() => (loading.value = data), 1000)
}
</script>
<style scoped>
.bgCard {
    background-color: #1E1E20;
}
.titleCard {
    color: white;
    text-align: center;
}
/* #F0580E orange color*/ 
.loadingColor {
    color: #F0580E;
}
</style>