<template>
  <div id="app">
    <AppHeader></AppHeader>
    <div class="container-fluid mt-4 page">
      <RouterView></RouterView>
    </div>
  </div>
</template>

<script>
import AppHeader from "./components/AppHeader.vue";
export default {
  components: {
    AppHeader,
  },
};
</script>

<style>
.page {
  max-width: 900px;
  margin: auto;
}
</style>
