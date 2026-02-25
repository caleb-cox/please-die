<template>
  <Die v-if="passKey" :pass-key @clearPassKey="() => (passKey = undefined)" />
  <PassKeyInput v-else @setPassKey="(value) => (passKey = value)" />
</template>

<script setup>
import { ref } from "vue";
import Die from "./components/Die.vue";
import PassKeyInput from "./components/PassKeyInput.vue";

const passKey = ref();
</script>
