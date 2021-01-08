<template>
  <div>
    <div>
      <form v-if="accName" @submit.prevent="dataFabric">
        <button type="submit">Try</button>
      </form>
      <div v-show="IError">
        <h1>Please check your Connection</h1>
      </div>
    </div>
    <div>
      <h1>In USD</h1>
      <form @submit.prevent="getResultOfExchange()">
        <label for="chooseCurrent"> Choose Current Exchainge </label>
        <select v-model="selected">
          <option
            v-for="exchange in data"
            :key="exchange.id"
            :value="exchange.rate"
          >
            {{ exchange.shorstName }}
          </option>
        </select>
        <input v-model="changeRates" type="number" />
        <button type="submit">Exchange Rates</button>
      </form>
      <div v-show="result">{{ changeRates }} is near ~~ <h1> {{ result }} </h1> <br> </div>
      <h1>In other Exchange</h1>
      <form @submit.prevent="getResultOfExchangeInt()">
        <label for="chooseCurrent"> From </label>
        <select v-model="selectedFrom">
          <option
            v-for="exchange in data"
            :key="exchange.id"
            :value="exchange.rate"
          >
            {{ exchange.shorstName }}
          </option>
        </select>
        <label>To</label>
        <select v-model="selectedTo">
          <option
            v-for="exchange in data"
            :key="exchange.id"
            :value="exchange.rate"
          >
            {{ exchange.shorstName }}
          </option>
        </select>
        <input v-model="intChange" type="number" />
        <button type="submit">Exchange Rates</button>
      </form>

      <div v-show="intResult">
        (from {{ selectedFrom }} to {{ selectedTo }}) /<br />
        {{ intChange }} near be ~~ <h1> {{ intResult }} </h1>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      data: [],
      acc: {},
      accName: {},
      lastUpdate: {},
      IError: false,
      changeRates: 0,
      selected: "",
      result: "",
      intChange: "",
      selectedTo: "",
      selectedFrom: "",
      intResult: "",
    };
  },
  methods: {
    getResultOfExchange() {
      this.result = this.selected * this.changeRates;
    },
    getDataRatesAndTimeUpdate() {
      axios
        .get(
          "http://api.currencylayer.com/live?access_key=ba0c1f4bc4e3afe63d553782f9971576"
        )
        .then(
          (response) => (
            (this.acc = response.data.quotes),
            (this.lastUpdate = response.data.timestamp)
          )
        )
        .catch((err) => console.log(err), (this.IError = true));
      return this.acc;
    },
    getDataRatesName() {
      axios
        .get(
          "http://api.currencylayer.com/list?access_key=ba0c1f4bc4e3afe63d553782f9971576"
        )
        .then((response) => (this.accName = response.data.currencies))
        .catch((err) => console.log(err), (this.accName = null));

      return this.accName;
    },
    dataFabric() {
      let fullNameAcc = Object.keys(this.accName);
      let shortNameAcc = Object.values(this.accName);
      let rateShortNane = Object.keys(this.acc);
      let rateExchange = Object.values(this.acc);
      for (let index = 0; index < Object.values(this.acc).length; index++) {
        this.data[index] = {
          id: index,
          name: fullNameAcc[index],
          shorstName: shortNameAcc[index],
          fullName: rateShortNane[index],
          rate: rateExchange[index],
        };
        this.$forceUpdate(this.data);
      }
    },
    getResultOfExchangeInt() {
      this.intResult = (this.selectedFrom / this.selectedTo) * this.intChange;
      this.$forceUpdate(this.intChange);
    },
  },
  mounted() {
    this.getDataRatesAndTimeUpdate();
    this.getDataRatesName();
    this.IError = false;
  },
};
</script>

<style lang="scss" scoped>
</style>

10467.391127
419.482046