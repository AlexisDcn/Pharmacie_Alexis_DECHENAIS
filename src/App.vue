<script setup>
import MedicamentList from "./components/MedicamentList.vue";
</script>

<template>
  <div id="app" class="app-style">
    <h1 class="title">La Pharmacie d'Alexis</h1>
    <MedicamentList />
  </div>
</template>

<style>
.app-style {
  font-family: Arial, sans-serif;
  text-align: center;
  background-color: #f8f9fa;
  min-height: 100vh;
  padding: 20px;
}
.title {
  margin-bottom: 20px;
  color: #333;
}
</style>
