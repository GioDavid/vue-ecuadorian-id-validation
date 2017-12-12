<template>
  <div id="demo" class="main-container">
      <h1 class="main-container__title">{{title}}</h1>
      <input class="main-container__input" type="Number"
      v-model="identification" />
      <button class="main-container__button"
      @click="validateIdentification">Validar</button>
      <br>
      <span class="main-container__text" >{{identification}}</span>
      <br>
      <span class="main-container__text" >{{identificationType}}</span>
  </div>
</template>

<script>

export default {
  name: 'demo',
  data () {
          return {
            title: 'Validación de Identificación ecuatoriana',
            identification:0,
            identificationType:'',
            type:'id_card',
            _thirdDigit :6,
            _minProvince : 1,
            _maxProvince : 22,
            _coefficients :["2", "1", "2", "1", "2", "1", "2", "1", "2"]
           }
      },
      methods:{
        validateIdentification () {
      	  this.identificationType = '';
     		  if(this.identification.length ===10 && this.validate(this.identification)) {
            this.identificationType = 'Es una cédula correcta'
            } else if(this.identification.length ===13 && this.validate(this.identification)){
                this.identificationType = 'Es un RUC correcto'
                } else {
                  this.identificationType = 'Es otro tipo de identificación'
                }
         },

        _between (x, min, max) {
                  return x >= min && x <= max
         },

      _getVerifyTotal (digits) {
                  var verifyNumber = digits[9]
                  var total = 0

                  for (var i = 0; i < this._coefficients.length; i++) {
                      var value = this._coefficients[i] * digits[i]
                      total = value >= 10 ? total + (value - 9) : total + value
                  }

                  var verifyDigit = total >= 10 ? (total % 10) != 0 ? 10 - (total % 10) : (total % 10) : total
                  return verifyNumber === String(verifyDigit)
        },

        _splitIdentification (identification) {
                  var numbers = []
                  return numbers = identification.split('')
        },

        _setNaturalPersonProperties (type) {
                  this.type = type
                  this._coefficients = ["2", "1", "2", "1", "2", "1", "2", "1", "2"]
                  this._thirdDigit = 6
                  this._minProvince = 1
                  this._maxProvince = 22
        },

        validateIdCard (idcard) {
                  var id = String(idcard)
                  var province = id.substring(0, 2)
                  var thirdNumber = id.substring(2, 3)
                  if (this._between(province, this._minProvince, this._maxProvince) && thirdNumber < this._thirdDigit) {
                      var digits = this._splitIdentification(idcard)
                      return this._getVerifyTotal(digits)
                  } else {
                      return false
                  }
        },

        validatePublicCard (value) {
                  var id = String(value)
                  var province = id.substring(0, 2)
                  var thirdNumber = id.substring(2, 3)
                  if (this._between(province, this._minProvince, this._maxProvince)) {
                      var digits = this._splitIdentification(value)
                      return this._getVerifyPublicIdentificationTotal(digits);
                  } else {
                      return false
                  }
              },

        _getVerifyPublicIdentificationTotal (digits) {
                  var verifyNumber = digits[8]
                  var total = 0
                  for (var i = 0; i < this._coefficients.length; i++) {
                      var value = this._coefficients[i] * digits[i]
                      total = total + value
                  }
                  var verifyDigit = 11 - (total % 11)
                  return verifyNumber === String(verifyDigit)
        },

        _checkBusiness (value) {
                  var businessTotal = value.substring(10, 13)
                  return businessTotal !== '000'
        },

        _setPublicRucProperties () {
                  this.type = 'public_ruc'
                  this._coefficients = ["3", "2", "7", "6", "5", "4", "3", "2"];
                  this._thirdDigit = 6
                  this._maxProvince = 22
        },

         _setJuridicalProperties () {
                  this.type = 'juridical_foreign_ruc'
                  this._coefficients = ["4", "3", "2", "7", "6", "5", "4", "3", "2"]
                  this._thirdDigit = 9
                  this._thirdDigit = 6
                  this._maxProvince = 22
        },

         validateJuridicalForeignCard (value) {
                  var id = String(value)
                  var province = id.substring(0, 2)
                  var thirdNumber = id.substring(2, 3)
                  if (this._between(province, this._minProvince, this._maxProvince)) {
                      var digits = this._splitIdentification(value)
                      return this._getVerifyJuridicalTotal(digits)
                  } else {
                      return false
                  }
              },

         _getVerifyJuridicalTotal: function(digits) {
                  var verifyNumber = digits[9]
                  var total = 0
                  for (var i = 0; i < this._coefficients.length; i++) {
                      var value = this._coefficients[i] * digits[i]
                      total = total + value
                  }
                  var verifyDigit = 11 - (total % 11)
                  return verifyNumber === String(verifyDigit)
              },

          validateRUC (value) {
                  var id = String(value)
                  var thirdNumber = id.substring(2, 3)
                  if (this._checkBusiness(id)) {
                      if (thirdNumber < 6) {
                      this._setNaturalPersonProperties('natural_person_ruc')
                          return this.validateIdCard(value)
                      } else {
                          switch (thirdNumber) {
                              case "6" || 6:
                                  this._setPublicRucProperties()
                                  return this.validatePublicCard(value)
                                  break
                              case "9" || 9:
                                  this._setJuridicalProperties()
                                  return this.validateJuridicalForeignCard(value)
                                  break
                          }
                      }
                  } else {
                      return false
                  }
              },


        validate: function(identification) {
                  if (identification.length === 13) {
                      return this.validateRUC(identification)
                  } else if (identification.length === 10) {
                      this._setNaturalPersonProperties('id_card')
                      return this.validateIdCard(identification)
                  } else {
                      return false
                  }
              }
      }

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
}

.main-container__title {
  margin: 24px;
  color: #B71C1C;
}

.main-container__input {
  margin-left: 24px;
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

.main-container__text {
    margin-left: 24px;
    margin-top: 16px;
}

</style>
