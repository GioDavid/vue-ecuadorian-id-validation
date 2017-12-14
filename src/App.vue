<template>
  <div id="app" class="main-container">
    <form action="/">
      <vue-ruc-input id="rucInput" ref="rucInput"></vue-ruc-input>
    </form>
    <button class="main-container__button" @click="validate" >Validate</button>
  </div>
</template>

<script>
  import VueRucInput from './components/vueRucInput.vue'

  export default {
    components: {'vue-ruc-input': VueRucInput},
    methods: {
      validate () {
        this.$refs.rucInput.validate()
        // return this.$.rucInput.validate()
      }
    },
    name: 'app'
  }
</script>

<style>
  .main-container {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    background-color: white;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    display: block;
  }

  .main-container__button {
    margin: 8px;
    margin-left: 24px;
    padding: 8px;

    overflow: hidden;

    border-width: 0;
    outline: none;
    border-radius: 2px;
    box-shadow: 0 1px 4px rgba(0, 0, 0, .6);

    background-color: #8BC34A;
    color: #ecf0f1;

    transition: background-color .3s;
  }
</style>
