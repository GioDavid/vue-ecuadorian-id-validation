<!--
@license
Copyright (c) 2017 David Proaño <davisxdpfr@gmail.com>. All rights reserved.
-->
<template>
  <div id="rucContainer" class="main-container">
      <input class="main-container__input" type= "text"
             pattern="\d*" maxlength="13" title="Ingrese solo digitos!"
             :required="isRequired"  ref="input"
             v-model="identification" :style="inputStyle"/>
      <span class="main-container__highlight"></span>
      <span class="main-container__bar"></span>
      <label class="main-container__label">{{label}}</label>
      <span class="main-container__error-message" v-if="!isValid">{{message}}</span>
      <ruc-validator ref="validator" @isValidChanged="isValid = $event"></ruc-validator>
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
        // Identification number of a person.
        identification: '',
        // Title to be display near the input.
        label: 'RUC',
        /**
         *  label to display over the input.
         */
        message: 'RUC incorrecto, solo se aceptan números!',
        /**
         *  boolean value use to show the error message
         */
        isValid: true,
        /**
         *  Object that is going to be bind to input style
         */
        inputStyle: {
          backgroundColor: '#fff',
          borderColor: '#212121',
          borderBottom: '1px solid'
        },
        /**
         *  Object that contain all the style properties for the input when the input value is correct
         */
        validInputStyle: {
          backgroundColor: '#fff',
          borderBottom: '2px solid #00E676'
        },
        /**
         *  Object that contain all the style properties for the input when there's an error
         */
        errorInputStyle: {
          backgroundColor: '#FFEBEE',
          borderBottom: '2px solid #B71C1C'
        },
        /**
         *  Boolean to set required to if rucInput is required
         */
        isRequired: false
      }
    },
    methods: {
      /**
       * Calls the validation method of the validator element, passing the identification to be validate.
       */
      validate () {
        // TODO check custom validation using setCustomValidity method in input
        this.$refs.validator.validate(this.identification)
      }
    },
    props: ['required'],
    watch: {
      // whenever isValid changes, this function will run
      isValid: function (value) {
        if (value) {
          this.$refs.input.setCustomValidity('')
          this.inputStyle = this.validInputStyle
        } else {
          this.$refs.input.setCustomValidity(this.message)
          this.inputStyle = this.errorInputStyle
        }
      }
    },

    name: 'vueRucInput'
  }
</script>
<style scoped>
  /* CONTAINER ------------------------------- */
  .main-container {
    position: relative;
    margin-bottom:45px;
    display: flex;
    flex-direction: column;
  }

  /* INPUT ======================================= */
  .main-container__input {
    font-size:18px;
    padding:10px 0px 10px 5px;
    display:block;
    width:300px;
    border:none;
    border-bottom:1px solid #757575;
  }

  .main-container__input:focus { outline:none; }

  /* LABEL ======================================= */
  .main-container__label{
    color:#999;
    font-size:18px;
    font-weight:normal;
    position:absolute;
    pointer-events:none;
    left:5px;
    top:10px;
    transition:0.2s ease all;
    -moz-transition:0.2s ease all;
    -webkit-transition:0.2s ease all;
  }

  /* active state */
  .main-container__input:focus ~ .main-container__label, .main-container__input:valid ~ .main-container__label  		{
    top:-20px;
    font-size:14px;
    color:#5264AE;
  }

  /* BOTTOM BARS ================================= */
  .main-container__bar 	{ position:relative; display:block; width:300px; }
  .main-container__bar:before, .main-container__bar:after 	{
    content:'';
    height:2px;
    width:0;
    bottom:1px;
    position:absolute;
    background:#5264AE;
    transition:0.2s ease all;
    -moz-transition:0.2s ease all;
    -webkit-transition:0.2s ease all;
  }
  .main-container__bar:before {
    left:50%;
  }
  .main-container__bar:after {
    right:50%;
  }

  /* active state */
  .main-container__input:focus ~ .main-container__bar:before, .main-container__input:focus ~ .main-container__bar:after {
    width:50%;
  }

  /* HIGHLIGHTER ================================== */
  .main-container__highlight {
    position:absolute;
    height:60%;
    width:100px;
    top:25%;
    left:0;
    pointer-events:none;
    opacity:0.5;
  }

  /* active state */
  .main-container__input:focus ~ .main-container__highlight {
    -webkit-animation:inputHighlighter 0.3s ease;
    -moz-animation:inputHighlighter 0.3s ease;
    animation:inputHighlighter 0.3s ease;
  }

  /* ANIMATIONS ================ */
  @-webkit-keyframes inputHighlighter {
    from { background:#5264AE; }
    to 	{ width:0; background:transparent; }
  }
  @-moz-keyframes inputHighlighter {
    from { background:#5264AE; }
    to 	{ width:0; background:transparent; }
  }
  @keyframes inputHighlighter {
    from { background:#5264AE; }
    to 	{ width:0; background:transparent; }
  }

  .main-container__error-message{
    color: #B71C1C;
    position:initial;
    width: 100%;
  }

</style>
