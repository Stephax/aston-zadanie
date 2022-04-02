<script setup>
import Calculator from "@/components/Calculator.vue";
import FormResult from "@/components/FormResult.vue";
</script>

<template>
  <div class="wrapper">
    <Calculator></Calculator>
  </div>
</template>

<style lang="scss" scoped>
.wrapper {
  display: flex;
  max-width: 900px;
  margin: 0 auto;
  @media (max-width: 800px) {
    display: block;
  }
}
</style>