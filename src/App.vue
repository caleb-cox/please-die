<template>
  <Die
    v-if="socketUrl"
    :socketUrl
    @clearSocketUrl="() => (socketUrl = undefined)"
  />
  <KeyInput v-else @setSocketUrl="(value) => (socketUrl = value)" />
</template>

<script setup>
import { ref } from "vue";
import Die from "./components/Die.vue";
import KeyInput from "./components/KeyInput.vue";

const socketUrl = ref();
</script>
