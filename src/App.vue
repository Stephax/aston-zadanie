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

.slide-up-enter-active {
  transition: all 0.3s ease-out;
}

.slide-up-leave-active {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-up-enter-from,
.slide-up-leave-to {
  transform: translateY(5px);
  opacity: 0;
}

.slide-to-left-enter-active {
  transition: all 0.3s ease-out;
}

.slide-to-left-leave-active {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-to-left-enter-from,
.slide-to-left-leave-to {
  transform: translateX(5px);
  opacity: 0;
}
</style>
