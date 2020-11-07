
<template>
  <main>
    <MusicApp />
    <Weather />
    <Calculator />
  </main>
</template>

<script>
import  MusicApp from './components/MusicApp.vue';
import Weather from './components/Weather.vue';
import  Calculator from './components/Calculator.vue';

export default {
  name: 'App',

  components: {
    MusicApp,
    Weather,
    Calculator,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  overflow-x: hidden;
  font-size: 16px;
}
#app {
  font-family: 'montserrat', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #f7f5f4;
}
</style>
