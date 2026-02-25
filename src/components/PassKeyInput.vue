<template>
  <form @submit.prevent="$emit('setPassKey', value)">
    <input v-model="value" />
  </form>
</template>

<script setup>
import { ref } from "vue";

defineEmits(["setPassKey"]);

const value = ref();
</script>

<style scoped>
form {
  background-color: var(--color-white);
  border-radius: 1.5em;
  padding: 1em;
}

input {
  width: 20em;
  border-radius: 0;
  outline: none;
  border: none;
  background-color: inherit;
  color: var(--color-black);
  font-family: inherit;
  font-size: inherit;
}
</style>
