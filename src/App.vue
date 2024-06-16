<template>
  <div id="app">
    <header>
      <h1>Интернет магазин</h1>
    </header>
    <main>
      <router-view />
    </main>
    <CategoryTree />
  </div>
</template>

<script>
import CategoryTree from './components/CategoryTree.vue';

export default {
  name: 'App',
  components: {
    CategoryTree
  }
};
</script>
<style>
body {
  font-family: 'Roboto', sans-serif;
  background-color: #f0f0f0;
}
</style>