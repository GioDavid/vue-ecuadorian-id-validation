<template>
</template>
<script>
  export default {
    data () {
      return {
        /**
         * Used to identify if the validation is going to be done to an idCard or a RUC.
         */
        type: 'id_card',
        /**
         * Used to verify what kind of identifier is.
         */
        thirdDigitValidator: 0,
        /**
         * Minimum province number in Ecuador.
         */
        _minProvince: 1,
        /**
         * Maximum province number in Ecuador.
         */
        _maxProvince: 22,
        /**
         * Verification coefficients of the ecuadorian idCard.
         */
        _verificationCoefficients: ['2', '1', '2', '1', '2', '1', '2', '1', '2']
      }
    },
    methods: {
      /**
       * Verify if the ruc is correct according to the ecuadorian id verification and it's length.
       * @param {Object} values The value to validate. May be any type depending on the validation logic.
       * @fireEvent isValidChanged when the the identification was validated.
       */
      validate (value) {
        var isValid = false
        if (value.length === 13) {
          var id = String(value)
          var thirdNumber = id.substring(2, 3)
          if (this._checkBusiness(id)) {
            if (thirdNumber < 6) {
              this._setNaturalPersonProperties('natural_person_ruc')
              isValid = this.validateIdCard(value)
            } else {
              switch (thirdNumber) {
                case '6' || 6:
                  this._setPublicRucProperties()
                  isValid = this.validatePublicCard(value)
                  break
                case '9' || 9:
                  this._setJuridicalProperties()
                  isValid = this.validateJuridicalForeignCard(value)
                  break
              }
            }
          }
        }
        this.$emit('isValidChanged', isValid)
      },

      /**
       * Compares if the x number is between the given min and max numbers.
       * @param {Number} value to compare.
       * @param {Number} min value to compare.
       * @param {Number} max value to compare.
       * @return {Boolean} true if `values` is valid.
       */
      _between: function (x, min, max) {
        return x >= min && x <= max
      },

      /**
       * Make a list with all the numbers contained in the identification.
       * @param {String} value to split.
       * @return {Array} the array with all the numbers contained in the identification.
       */
      _splitIdentification: function (identification) {
        var numbers = (identification) ? identification.split('') : []
        return numbers
      },

      /**
       * validates that the business has at least one business.
       * @param {Number} identification.
       * @return {Boolean} true if total number of businesses are no zero.
       */
      _checkBusiness (value) {
        var businessTotal = value.substring(10, 13)
        return businessTotal !== '000'
      },

      /**
       * Sets the properties necessary for the operations to validate the natural id card.
       * @param {String} type to be set for a natural person id or natural person ruc.
       */
      _setNaturalPersonProperties (type) {
        this.type = type
        this._verificationCoefficients = ['2', '1', '2', '1', '2', '1', '2', '1', '2']
        this.thirdDigitValidator = 6
        this._minProvince = 1
        this._maxProvince = 22
      },

      /**
       * Sets the properties necessary for the operations to validate the public ruc card.
       */
      _setPublicRucProperties () {
        this.type = 'public_ruc'
        this._coefficients = ['3', '2', '7', '6', '5', '4', '3', '2']
        this.thirdDigitValidator = 6
        this._minProvince = 1
        this._maxProvince = 22
      },

      /**
       * this function verify that the result of the validation operations of the idCard with the verifyNumber contained
       * in the list, in this case the 10th digit are equal
       * @param {Array} list of the numbers contained in the ecuadorian idCard.
       * @return {Boolean} true if the result of the operation is equal to the 10th digit.
       */
      _getVerifyTotal (digits) {
        var verifyNumber = digits[9]
        var total = 0

        for (var i = 0; i < this._verificationCoefficients.length; i++) {
          var value = this._verificationCoefficients[i] * digits[i]
          total = value >= 10 ? total + (value - 9) : total + value
        }

        var verifyDigit = total >= 10 ? (total % 10) !== 0 ? 10 - (total % 10) : (total % 10) : total
        return verifyNumber === String(verifyDigit)
      },

      /**
       * Verify if the idCard is correct according to the ecuadorian id verification.
       * @param {Object} values The value to validate. May be any type depending on the validation logic.
       * @return {Boolean} true if `idCard` is valid.
       */
      validateIdCard (idcard) {
        var id = String(idcard)
        var province = id.substring(0, 2)
        var thirdNumber = id.substring(2, 3)
        if (this._between(province, this._minProvince, this._maxProvince) && thirdNumber < this.thirdDigitValidator) {
          var digits = this._splitIdentification(idcard)
          return this._getVerifyTotal(digits)
        } else {
          return false
        }
      },

      /**
       * Verify if the public identification is correct according to the ecuadorian verification.
       * @param {Object} values The value to validate. May be any type depending on the validation logic.
       * @return {Boolean} publicCard if `values` is valid.
       */
      validatePublicCard (publicCard) {
        var id = String(publicCard)
        var province = id.substring(0, 2)
        if (this._between(province, this._minProvince, this._maxProvince)) {
          var digits = this._splitIdentification(publicCard)
          return this._getVerifyPublicIdentificationTotal(digits)
        } else {
          return false
        }
      },

      /**
       * This function verify that the result of the validation operations of ecuadorian public identification with the
       * verifyNumber contained in the list, in this case the 9h digit are equal
       * @param {Array} list of the numbers contained in the ecuadorian idCard.
       * @return {Boolean} true if the result of the operation is equal to the 9th digit.
       */
      _getVerifyPublicIdentificationTotal (digits) {
        var verifyNumber = digits[8]
        var total = 0
        for (var i = 0; i < this._verificationCoefficients.length; i++) {
          var value = this._verificationCoefficients[i] * digits[i]
          total = total + value
        }
        var verifyDigit = 11 - (total % 11)
        return verifyNumber === String(verifyDigit)
      },

      /**
       * Sets the properties necessary for the operations to validate the juridical or foreign id card.
       */
      _setJuridicalProperties () {
        this.type = 'juridical_foreign_ruc'
        this._verificationCoefficients = ['4', '3', '2', '7', '6', '5', '4', '3', '2']
        this._thirdDigit = 9
        this._minProvince = 1
        this._maxProvince = 22
      },

      /**
       * Verify if the juridicial or foreign identification is correct according to the ecuadorian verification.
       * @param {Object} values The value to validate. May be any type depending on the validation logic.
       * @return {Boolean} true if `juridicaForeignCard` is valid.
       */
      validateJuridicalForeignCard (juridicaForeignCard) {
        var id = String(juridicaForeignCard)
        var province = id.substring(0, 2)
        if (this._between(province, this._minProvince, this._maxProvince)) {
          var digits = this._splitIdentification(juridicaForeignCard)
          return this._getVerifyJuridicalTotal(digits)
        } else {
          return false
        }
      },

      /**
       * This function verify that the result of the validation operations of ecuadorian juridical with the verifyNumber contained
       * in the list, in this case the 10th digit are equal
       * @param {Array} list of the numbers contained in the ecuadorian idCard.
       * @return {Boolean} true if the result of the operation is equal to the 10th digit.
       */
      _getVerifyJuridicalTotal (digits) {
        var verifyNumber = digits[9]
        var total = 0
        for (var i = 0; i < this._verificationCoefficients.length; i++) {
          var value = this._verificationCoefficients[i] * digits[i]
          total = total + value
        }
        var verifyDigit = 11 - (total % 11)
        return verifyNumber === String(verifyDigit)
      }
    }
  }
</script>

