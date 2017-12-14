<template>
  <div id="rucContainer" class="main-container">
    <label class="main-container__text main-container__label">{{label}}</label>
    <input class="main-container__input" type= "text"
           pattern="\d*" maxlength="13" title="Ingrese solo digitos!" v-model="identification" />
    <ruc-validator ref="validator" @isValidChanged="isValid = $event"></ruc-validator>
    <br>
    <span class="main-container__text main-container__error-message" v-if="!isValid">{{message}}</span>
  </div>
</template>
<script>
  import rucValidator from './rucValidator.vue'

  export default {
    components: {
      'ruc-validator': rucValidator
    },
    data () {
      return {
        /**
         *  identification number of a person.
         */
        identification: '',
        /**
         *  title to be display near the input.
         */
        label: 'RUC',
        /**
         *  error message to show.
         */
        message: 'RUC incorrecto!',
        /**
         *  boolean value use to show the error message
         */
        isValid: true
      }
    },
    methods: {
      validate () {
        this.$refs.validator.validate(this.identification)
      }
    },
    name: 'vueRucInput'
  }
</script>
<style scoped>
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

  main-container__label{
    color: #B71C1C;
  }

  .main-container__title {
    margin: 24px;
    color: #B71C1C;
  }

  .main-container__input {
    margin-left: 24px;
  }

  .main-container__text {
    margin-left: 24px;
    margin-top: 16px;
  }

  .main-container__error-message{
    color: #B71C1C;
  }
</style>
