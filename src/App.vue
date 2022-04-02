<script setup>
import { RouterLink, RouterView } from "vue-router";
import HelloWorld from "@/components/HelloWorld.vue";
import Calculator from "@/components/Calculator.vue";
</script>

<template>
  <RouterView />
</template>

<style lang="scss">
@import "@/assets/base.css";
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@300;500;700&display=swap");
#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
  font-family: "Roboto", sans-serif;
  font-weight: normal;
  @media (max-width: 800px) {
    padding: 0 15px;
  }
}
</style>
