<template>
  <Die v-if="socketUrl" :socketUrl />
  <SocketUrlInput v-else @submit="(value) => (socketUrl = value)" />
</template>

<script setup>
import { ref } from "vue";
import Die from "./components/Die.vue";
import SocketUrlInput from "./components/SocketUrlInput.vue";

const socketUrl = ref();

const onSubmit = () => {
  socketUrl.value = inputValue.value;
};
</script>
