<template>
  <main>
    <Header />
    <RouterView />
    <Footer />
  </main>
</template>


<script>
  import { RouterView } from 'vue-router';
  import Header from './shared/Header.vue';
  import Footer from './shared/Footer.vue';

  export default {
    components: {
      Header,
      Footer
    }
  }
</script>


<style scoped lang="scss">
  @import "./assets/sass/main.scss";

  main {
    min-height: calc(100vh - 120px);
    font-family: "Nunito";
    background-color: $background-color;
  }
</style>
