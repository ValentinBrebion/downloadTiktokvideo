<template>
    <v-container>
      <h1>{{ msg }}</h1>
    </v-container>
</template>
<script setup lang="ts">
defineProps({
  msg: {
    type: String,
    required: true
  }
})
</script>