<script setup>
import InsuranceCalculator from "@/components/InsuranceCalculator.vue";
</script>

<template>
  <div class="wrapper">
    <InsuranceCalculator></InsuranceCalculator>
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
