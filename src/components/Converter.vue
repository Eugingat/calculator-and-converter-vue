<template>
  <div :class="{ converter: true, light: isDark }" @input="changeRate">
    <div v-for="rate of listCurrency" :key="rate.Cur_ID" :class="{ currencyInput: true, currencyInputLight: isDark }">
      <label> {{ rate.Cur_Abbreviation }}</label>
      <input type="text" v-model="fieildInputs[rate.Cur_Abbreviation]" :name="rate.Cur_Abbreviation">
      <svg-icon type="mdi" :path="pathDelIcon" v-if="rate.del" @click="delRate(rate)"></svg-icon>
    </div>
    <button :class="{ addCurrencyBtn: true, addCurrencyBtnLight: isDark }"
            @click="isListNewCurrency = !isListNewCurrency"> Add currency
    </button>
    <div class="blockCurrency">
      <div :class="{listNewCurrency: true, visibleListNewCurrency: isListNewCurrency, light: isDark}">
        <div :class="{ search: true, searchLight: isDark }">
          <label> Search:</label>
          <input class="searchInput" type="search" v-model="searchCurrency" placeholder="Write name currency">
        </div>
        <div v-for="currency in searchedListCurrency" :key="currency.Cur_ID"
             :class="{ newCurrency: true, newCurrencyLight: isDark }"
             @click="addCurrency(currency)">
          {{ currency.Cur_Name }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';
import SvgIcon from '@jamescoyle/vue-icon';
import {mdiDeleteForever} from '@mdi/js';

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Converter",
  components: {
    SvgIcon
  },
  data() {
    return {
      defaultListCurrency: ['USD', 'BYN', 'EUR', 'RUB', 'UAH', 'PLN', 'TRY'],
      listCurrency: [],
      listAllCurrensy: [],
      isListNewCurrency: false,
      searchedListCurrency: [],
      pathDelIcon: mdiDeleteForever,
      searchCurrency: '',
      fieildInputs: {},
      rate: {},
    }
  },
  inject: ['theme'],
  computed: {
    isDark() {
      return this.theme.value === 'dark'
    }
  },
  async mounted() {
    await this.getAllCurrency()
  },
  watch: {
    searchCurrency(value) {
      if (value.trim()) {
        this.searchedListCurrency = this.listAllCurrensy.filter(currency => currency.Cur_Name.toLowerCase().includes(value.toLowerCase()))
      } else {
        this.searchedListCurrency = [...this.listAllCurrensy];
      }
    },
  },
  methods: {
    async getAllCurrency() {
      const currentDate = moment().format();
      const response = await fetch('https://www.nbrb.by/api/exrates/currencies');
      const allCurrency = await response.json();

      const filteredCurrentCurrency = allCurrency.filter(currency => this.defaultListCurrency.includes(currency.Cur_Abbreviation) && moment(currentDate).isBetween(currency.Cur_DateStart, currency.Cur_DateEnd));
      this.listAllCurrensy = allCurrency.filter(currency => !this.defaultListCurrency.includes(currency.Cur_Abbreviation) && moment(currentDate).isBetween(currency.Cur_DateStart, currency.Cur_DateEnd))

      const responseRates = await Promise.all(filteredCurrentCurrency.map(currency => fetch(`https://www.nbrb.by/api/exrates/rates/${currency.Cur_ID}`)));
      this.listCurrency = await Promise.all(responseRates.map(response => response.json()));

      const rateBYN = {
        Cur_Abbreviation: 'BYN',
        Cur_ID: -1,
      }

      this.listCurrency = [...this.listCurrency, rateBYN]

      this.searchedListCurrency = [...this.listAllCurrensy];

      this.rate = this.listCurrency.find(currency => currency.Cur_Abbreviation === 'USD');

      this.listCurrency.forEach(currency => {
        if (currency.Cur_Abbreviation === 'USD') {
          this.fieildInputs[currency.Cur_Abbreviation] = 1;
        } else if (currency.Cur_Abbreviation === 'BYN') {
          this.fieildInputs[currency.Cur_Abbreviation] = +this.rate.Cur_OfficialRate;
        } else {
          this.fieildInputs[currency.Cur_Abbreviation] = +((this.rate.Cur_OfficialRate / currency.Cur_OfficialRate * currency.Cur_Scale).toFixed(4));
        }
      })
    },
    async addCurrency(currency) {
      const response = await fetch(`https://www.nbrb.by/api/exrates/rates/${currency.Cur_ID}`);
      const rate = await response.json();

      rate.del = true;
      this.isListNewCurrency = false;
      await new Promise((resolve) => {
        setTimeout(() => {
          this.listCurrency = [...this.listCurrency, rate]
          resolve();
        }, 500);
      });
      this.changeCountAfterAddRate();
      this.listAllCurrensy = this.listAllCurrensy.filter(currentCurrency => currency !== currentCurrency);
      this.searchedListCurrency = [...this.listAllCurrensy];
    },
    delRate(rate) {
      this.listCurrency = this.listCurrency.filter(currensy => currensy !== rate);
      this.listAllCurrensy = [...this.listAllCurrensy, rate];
      this.searchedListCurrency = [...this.listAllCurrensy];
    },
    changeRate(event) {
      this.rate = this.listCurrency.find(currency => currency.Cur_Abbreviation === event.target.name);

      this.reCountValueRates(event.target.value);

    },
    changeCountAfterAddRate() {
      const value = this.fieildInputs[this.rate.Cur_Abbreviation];

      this.reCountValueRates(value);
    },
    reCountValueRates(value) {
      if (this.rate.Cur_Abbreviation !== 'BYN') {
        if (this.rate.Cur_Scale === 1) {
          this.listCurrency.forEach(currency => {
            if (currency.Cur_Abbreviation === 'BYN') {
              this.fieildInputs[currency.Cur_Abbreviation] = +(value * this.rate.Cur_OfficialRate).toFixed(4);
            } else if (currency.Cur_Abbreviation !== 'BYN' && currency.Cur_Abbreviation !== this.rate.Cur_Abbreviation) {
              this.fieildInputs[currency.Cur_Abbreviation] = +(((value * this.rate.Cur_OfficialRate) / currency.Cur_OfficialRate) * currency.Cur_Scale).toFixed(4);
            }
          })
        } else {
          this.listCurrency.forEach(currency => {
            if (currency.Cur_Abbreviation === 'BYN') {
              this.fieildInputs[currency.Cur_Abbreviation] = +((value * this.rate.Cur_OfficialRate) / this.rate.Cur_Scale).toFixed(4);
            } else if (currency.Cur_Abbreviation !== 'BYN' && currency.Cur_Abbreviation !== this.rate.Cur_Abbreviation) {
              this.fieildInputs[currency.Cur_Abbreviation] = +((((value * this.rate.Cur_OfficialRate) / currency.Cur_OfficialRate) * currency.Cur_Scale) / this.rate.Cur_Scale).toFixed(4);
            }
          })
        }
      } else {
        this.listCurrency.forEach(currency => {
          if (currency.Cur_Abbreviation === 'BYN') {
            this.fieildInputs[currency.Cur_Abbreviation] = +(Number(value).toFixed(4));
          } else if (currency.Cur_Abbreviation !== 'BYN' && currency.Cur_Abbreviation !== this.rate.Cur_Abbreviation) {
            this.fieildInputs[currency.Cur_Abbreviation] = +((value / currency.Cur_OfficialRate) * currency.Cur_Scale).toFixed(4);
          }
        })
      }
    }
  }
}
</script>

<style scoped>
.search {
  display: flex;
  margin: 10px 10px 10px 15px;
}


.search label {
  color: #f5f5f5;
  font-size: 20px;
  margin-right: 10px;
  align-self: center;
  transition-duration: 1s;
}

.searchLight label {
  color: #009788!important;
}

.searchLight input {
  border: 2px solid black;
  color: #009788;
}


.searchInput {
  width: 100%;
  font-size: 16px;
  border: 2px solid #283637;
  background-color: #f5f5f5;
  padding: 10px 0 10px 15px;
}

.searchInput::-webkit-search-cancel-button {
  -webkit-appearance: none;
  background-image: url('@/assets/close.svg');
  width: 16px;
  height: 16px;
  color: #009788;
  margin-right: 10px;
  cursor: pointer;
}

.converter {
  max-width: 600px;
  margin: 0 auto;
  background: #283637;
  box-shadow: 2px 2px 2px #283637;
  padding: 5px 10px;
  transition-duration: 1s;
}

.currencyInput {
  color: #f5f5f5;
  width: 100%;
  margin: 15px 0;
  font-size: 22px;
  display: grid;
  grid-template-columns: 70px 1fr 20px;
  grid-gap: 10px;
  transition-duration: 1s;
}

.currencyInput input {
  background-color: #f5f5f5;
  border: 2px solid #283637;
  font-size: 20px;
  padding-left: 7px;
}

.currencyInputLight {
  color: #009788;
}

.currencyInputLight input {
  color: #009788;
  border: 2px solid black;
}

.addCurrencyBtn {
  border: 2px solid black;
  background: #283637;
  color: #f5f5f5;
  font-size: 16px;
  padding: 10px 15px;
  cursor: pointer;
  transition-duration: 1s;
}

.addCurrencyBtnLight {
  background-color: #f5f5f5;
  color: #009788;
}

.listNewCurrency {
  width: 620px;
  height: 0px;
  box-shadow: 2px 2px 2px #283637;
  background: #283637;
  position: absolute;
  top: 0;
  left: 50%;
  transform: translate(-50%, 0);
  overflow-y: scroll;
  transition-duration: 1s;
}

.listNewCurrency::-webkit-scrollbar {
  width: 10px;
  background-color: #f5f5f5;
  border-radius: 25px;
}

.listNewCurrency::-webkit-scrollbar-thumb {
  width: 10px;
  background-color: #009788;
  border-radius: 25px;
}

.visibleListNewCurrency {
  height: 350px;
}

.newCurrency {
  font-size: 16px;
  margin: 10px;
  padding: 10px 15px;
  border: 2px solid #f5f5f5;
  background: #283637;
  color: #f5f5f5;
  cursor: pointer;
  transition-duration: 1s;
}

.newCurrencyLight {
  border: 2px solid #283637;
  background: #f5f5f5;
  color: #009788;
}

.blockCurrency {
  position: relative;
  margin-top: 10px;
}

</style>