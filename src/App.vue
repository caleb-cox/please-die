<template>
  <Die v-if="socketUrl" :socketUrl />
  <KeyInput
    v-else
    @submit="(value) => (socketUrl = `https://${value}.loca.lt`)"
  />
</template>

<script setup>
import { ref } from "vue";
import Die from "./components/Die.vue";
import KeyInput from "./components/KeyInput.vue";

const socketUrl = ref();

const onSubmit = () => {
  socketUrl.value = inputValue.value;
};
</script>
