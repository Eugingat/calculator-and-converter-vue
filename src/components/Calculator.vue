<template>
  <div :class="{ calc: true, light: isDark }">
    <input v-model="valueCalc" disabled :class="{fieldInput: true, inputLight: isDark}">
    <div :class="{ funcCalc: true }">
      <button :class="{ btnCalc: true, btnLight: isDark }"
              v-for="({ symbol, icon , method, desciptionSymbol }) of listBtnCacl"
              :key="symbol"
              @click="method(desciptionSymbol)"
              @mouseenter="mouseEnterBtn"
              @mouseleave="mouseLeaveBtn"
      >
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
      generalMathSymbols: [' + ', ' - ', ' * ', ' / '],
      regExpGeneralMathSymbols: /\s\+\s|\s-\s|\s\*\s|\s\/\s|=/gi,
      listBtnCacl: [
        {
          symbol: mdiPercentOutline,
          icon: true,
          method: this.specMathOperation,
          desciptionSymbol: '%'
        },
        {
          symbol: 'CE',
          icon: false,
          method: this.clearLastValue,
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
          method: this.specMathOperation,
          desciptionSymbol: '1/x'
        }, {
          symbol: mdiFormatSuperscript,
          icon: true,
          method: this.specMathOperation,
          desciptionSymbol: 'x2'
        },
        {
          symbol: mdiSquareRoot,
          icon: true,
          method: this.specMathOperation,
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
          method: this.specMathOperation,
          desciptionSymbol: 'plusMinusVariant'
        },
        {
          symbol: '0',
          icon: false,
          method: this.addValue,
          desciptionSymbol: '0'
        },
        {
          symbol: '.',
          icon: false,
          method: this.decimalSymbol,
          desciptionSymbol: '.'
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
  mounted() {
    window.addEventListener('keyup', this.eventNumpad)
  },
  unmounted() {
    window.removeEventListener('keyup', this.eventNumpad)
  },
  methods: {
    eventNumpad(event) {
      switch (event.code) {
        case 'Numpad1':
        case 'Numpad2':
        case 'Numpad3':
        case 'Numpad4':
        case 'Numpad5':
        case 'Numpad6':
        case 'Numpad7':
        case 'Numpad8':
        case 'Numpad9':
        case 'Numpad0':
          return this.addValue(event.key);
        case 'NumpadAdd':
        case 'NumpadSubtract':
        case 'NumpadMultiply':
        case 'NumpadDivide':
          return this.generealMathOperation(event.key);
        case 'NumpadEnter':
          return this.toEqual();
        case 'Backspace':
          return this.delLastValue();
      }
    },
    clearValueCacl() {
      this.valueCalc = '0';
    },
    clearLastValue() {
      const arrValue = this.valueCalc.split(this.regExpGeneralMathSymbols);

      if (arrValue.length === 2) {
        const [operator] = this.valueCalc.match(this.regExpGeneralMathSymbols);
        arrValue[1] = '0'
        this.valueCalc = arrValue.join(operator);
      } else {
        this.valueCalc = '0';
      }
    },
    delLastValue() {
      if (this.valueCalc.includes('=')) {
        this.valueCalc = this.valueCalc.split('=')[1].trim();
      } else {
        if (this.valueCalc.at(-1).trim()) {
          this.valueCalc = this.valueCalc.substring(0, this.valueCalc.length - 1);
        } else {
          this.valueCalc = this.valueCalc.substring(0, this.valueCalc.length - 3);
        }
      }
    },
    addValue(value) {
      if (this.valueCalc === '0') {
        this.valueCalc = value;
      } else {
        this.valueCalc += value;
      }
    },
    generealMathOperation(value) {
      if (this.regExpGeneralMathSymbols.test(this.valueCalc)) {
        const arrValue = this.valueCalc.split(this.regExpGeneralMathSymbols);

        if (arrValue.at(-1)) {
          const [operator, equal] = this.valueCalc.match(this.regExpGeneralMathSymbols);
          if (equal) {
            this.valueCalc = arrValue[2].trim() + ` ${value} `;
          } else {
            this.valueCalc = this.countValue(arrValue, operator) + ` ${value} `;
          }
        } else {
          this.valueCalc = this.valueCalc.replace(this.regExpGeneralMathSymbols, ` ${value} `)
        }
      } else {
        const arrValue = this.valueCalc.split(this.regExpGeneralMathSymbols);

        if (arrValue.length === 2) {
          const [operator] = this.valueCalc.match(this.regExpGeneralMathSymbols);

          this.valueCalc = this.countValue(arrValue, operator) + ` ${value} `;
        } else {
          this.valueCalc += ` ${value} `;
        }
      }
    },
    decimalSymbol() {
      if (this.regExpGeneralMathSymbols.test(this.valueCalc)) {
        const arrValue = this.valueCalc.split(this.regExpGeneralMathSymbols);

        if (arrValue.at(-1)) {
          const [operator] = this.valueCalc.match(this.regExpGeneralMathSymbols);

          if (!arrValue.at(-1).includes('.')) {
            arrValue[1] += '.';

            this.valueCalc = arrValue.join(` ${operator} `);
          }
        } else {
          this.valueCalc += '0.'
        }
      } else {
        if (!this.valueCalc.includes('.')) {
          this.valueCalc += '.';
        }
      }
    },
    specMathOperation(specOperator) {
      const arrValue = this.valueCalc.split(this.regExpGeneralMathSymbols);

      if (arrValue.length === 2) {
        const [mathOperator] = this.valueCalc.match(this.regExpGeneralMathSymbols);

        if (!arrValue[1].trim()) {
          this.valueCalc = this.countValueSpecOperators(specOperator, arrValue[0]) + ` ${mathOperator} `;
        } else {
          arrValue[1] = this.countValueSpecOperators(specOperator, arrValue[1]);

          this.valueCalc = arrValue.map((value) => value.trim()).join(` ${mathOperator} `)
        }
      } else if (arrValue.length === 3) {
        this.valueCalc = this.countValueSpecOperators(specOperator, arrValue[2]);
      } else {
        this.valueCalc = this.countValueSpecOperators(specOperator, arrValue[0]);
      }
      //'√'
    },

    countValueSpecOperators(specOperator, value) {
      switch (specOperator) {
        case '1/x':
          return `${1 / value}`;
        case 'x2':
          return `${value ** 2}`;
        case 'squareRoot':
          return `${Math.sqrt(value)}`;
        case 'plusMinusVariant':
          if (Math.sign(this.valueCalc)) {
            return `${-1 * value}`;
          } else {
            return `${-1 * value}`;
          }
      }
    },
    toEqual() {
      if (this.regExpGeneralMathSymbols.test(this.valueCalc)) {
        const arrValue = this.valueCalc.split(this.regExpGeneralMathSymbols);
        const [operator, equal] = this.valueCalc.match(this.regExpGeneralMathSymbols);
        const indexOperator = this.valueCalc.indexOf(operator);
        const indexEqual = this.valueCalc.indexOf(equal);

        if (arrValue.length === 2) {
          this.valueCalc += ' = ';
          this.valueCalc += this.countValue(arrValue, operator);
        }

        if (arrValue.length === 3) {
          const substrStart = this.valueCalc.slice(0, indexOperator);
          const substrEnd = this.valueCalc.slice(indexEqual + 1);

          this.valueCalc = this.valueCalc.replace(substrStart, substrEnd);

          this.valueCalc = this.valueCalc.substring(0, this.valueCalc.length - arrValue.at(-1).length) + ' ' + this.countValue(this.valueCalc.split(this.regExpGeneralMathSymbols), operator);
        }
      }
    },
    countValue([firstValue, secondValue], operator) {
      switch (operator.trim()) {
        case '+':
          return +firstValue + +secondValue;
        case '-':
          return +firstValue - +secondValue;
        case '*':
          return +firstValue * +secondValue;
        case '/':
          return +firstValue / +secondValue;
      }
    },
    mouseEnterBtn(event) {
      event.target.classList.toggle('btnLight');
    },
    mouseLeaveBtn() {
      event.target.classList.toggle('btnLight');
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
  cursor: pointer;
  transition-duration: 1s;
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

.btnLight {
  background: #f5f5f5 !important;
  color: #009788;
}
</style>