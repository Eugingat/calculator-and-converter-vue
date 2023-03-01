<template>
  <div :class="{ calc: true, light: isDark }">
    <input v-model="valueCalc" disabled :class="{fieldInput: true, inputLight: isDark}">
    <div :class="{ funcCalc: true }">
      <button :class="{ btnCalc: true }" v-for="({ symbol, icon , method, desciptionSymbol }) of listBtnCacl"
              :key="symbol"
              @click="method(desciptionSymbol)">
        <span v-if="!icon">
          {{ symbol }}
        </span>
        <SvgIcon v-else type="mdi" :path="symbol"></SvgIcon>
      </button>
    </div>
  </div>
</template>

<script>
import SvgIcon from '@jamescoyle/vue-icon';
import {
  mdiBackspaceOutline,
  mdiSquareRoot,
  mdiPercentOutline,
  mdiClose,
  mdiDivision,
  mdiFormatSuperscript,
  mdiMinus,
  mdiPlus,
  mdiPlusMinusVariant,
  mdiEqual
} from '@mdi/js';

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Calculator",
  components: {
    SvgIcon
  },
  data() {
    return {
      valueCalc: '',
      generalMathSymbols: ['+', '-', '*', '/'],
      regExpGeneralMathSymbols: /\+|-|\*|\/|=/gi,
      listBtnCacl: [
        {
          symbol: mdiPercentOutline,
          icon: true,
          method: this.clearValueCacl,
          desciptionSymbol: '%'
        },
        {
          symbol: 'CE',
          icon: false,
          method: this.clearValueCacl,
          desciptionSymbol: 'CE'
        },
        {
          symbol: 'C',
          icon: false,
          method: this.clearValueCacl,
          desciptionSymbol: 'C'
        }, {
          symbol: mdiBackspaceOutline,
          icon: true,
          method: this.delLastValue,
          desciptionSymbol: 'delLastSymbol'
        },
        {
          symbol: '1/x',
          icon: false,
          method: this.delLastValue,
          desciptionSymbol: '1/x'
        }, {
          symbol: mdiFormatSuperscript,
          icon: true,
          method: this.delLastValue,
          desciptionSymbol: 'x2'
        },
        {
          symbol: mdiSquareRoot,
          icon: true,
          method: this.delLastValue,
          desciptionSymbol: 'squareRoot'
        }, {
          symbol: mdiDivision,
          icon: true,
          method: this.generealMathOperation,
          desciptionSymbol: '/'
        }, {
          symbol: '7',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '7'
        }, {
          symbol: '8',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '8'
        }, {
          symbol: '9',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '9'
        }, {
          symbol: mdiClose,
          icon: true,
          method: this.generealMathOperation,
          desciptionSymbol: '*'
        },
        {
          symbol: '4',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '4'
        },
        {
          symbol: '5',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '5'
        },
        {
          symbol: '6',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '6'
        }, {
          symbol: mdiMinus,
          icon: true,
          method: this.generealMathOperation,
          desciptionSymbol: '-'
        }, {
          symbol: '1',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '1'
        },
        {
          symbol: '2',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '2'
        },
        {
          symbol: '3',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '3'
        },
        {
          symbol: mdiPlus,
          icon: true,
          method: this.generealMathOperation,
          desciptionSymbol: '+'
        },
        {
          symbol: mdiPlusMinusVariant,
          icon: true,
          method: this.delLastValue,
          desciptionSymbol: 'plusMinusVariant'
        },
        {
          symbol: '0',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '0'
        },
        {
          symbol: ',',
          icon: false,
          method: this.generealMathOperation,
          desciptionSymbol: ','
        },
        {
          symbol: mdiEqual,
          icon: true,
          method: this.toEqual,
          desciptionSymbol: '='
        },
      ],
      pathDeleteIcon: mdiBackspaceOutline
    }
  },
  inject: ['theme'],
  computed: {
    isDark() {
      return this.theme.value === 'dark'
    }
  },
  methods: {
    clearValueCacl() {
      this.valueCalc = '';
    },
    delLastValue() {
      this.valueCalc = this.valueCalc.substring(0, this.valueCalc.length - 1)
    },
    addValue(value) {
      this.valueCalc += value;
    },
    generealMathOperation(value) {
      if (this.regExpGeneralMathSymbols.test(this.valueCalc.at(-1))) {
        this.valueCalc = this.valueCalc.replace(this.regExpGeneralMathSymbols, value)
      } else {
        this.valueCalc += value;
      }
    },
    toEqual() {
      if (this.regExpGeneralMathSymbols.test(this.valueCalc)) {
        const arrValue = this.valueCalc.split(this.regExpGeneralMathSymbols);
        const [ operator, equal ] = this.valueCalc.match(this.regExpGeneralMathSymbols);
        const indexOperator = this.valueCalc.indexOf(operator);
        const indexEqual = this.valueCalc.indexOf(equal);

        console.log(this.valueCalc.match(this.regExpGeneralMathSymbols))

        if (arrValue.length === 2) {
          this.valueCalc += '=';
          this.valueCalc += this.countValue(arrValue, operator);
        }

        if (arrValue.length === 3) {
          const substrStart = this.valueCalc.slice(0, indexOperator);
          const substrEnd = this.valueCalc.slice(indexEqual + 1);

          this.valueCalc = this.valueCalc.replace(substrStart, substrEnd);

          this.valueCalc = this.valueCalc.substring(0, this.valueCalc.length - arrValue.at(-1).length) + this.countValue(this.valueCalc.split(this.regExpGeneralMathSymbols), operator);
        }
      }
    },
    countValue([firstValue, secondValue], operator) {
      if (operator === '+') {
        return +firstValue + +secondValue
      } else if (operator === '-') {
        return +firstValue - +secondValue;
      } else if (operator === '*') {
        return +firstValue * +secondValue;
      } else if (operator === '/') {
        return +firstValue / +secondValue;
      }
    }
  },
}
</script>

<style scoped>
.calc {
  max-width: 600px;
  margin: 0 auto;
  background: #283637;
  box-shadow: 2px 2px 2px #283637;
  padding: 5px 10px;
  transition-duration: 1s;
}

.btnCalc {
  background: #283637;
  color: #f5f5f5;
  padding: 15px;
}

.btnCalc span {
  font-weight: 500;
  font-size: 20px;
}

.funcCalc {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 10px;
  transition-duration: 1s;
}

.fieldInput {
  border: none;
  width: calc(100% - 30px);
  min-height: 50px;
  text-align: right;
  padding: 0 15px;
  background: #283637;
  color: #f5f5f5;
  font-size: 32px;
  margin: 10px 0;
  transition-duration: 1s;
}

.inputLight {
  background: #f5f5f5;
  color: #009788;
}
</style>