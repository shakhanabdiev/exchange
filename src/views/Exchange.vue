<template>
  <div>
    <div v-if="ratesName">
      <div >
        <div>
          <h1>Last Update:</h1>
          <p>
            <strong>{{ toLocaltime }}</strong>
          </p>
          <br />
            <h3>Choose youre main stocks</h3>
            <p>Click on the price of stocks</p>
          <div v-if="!choosenStock >= 2">
          </div>
          <div v-else>
            <ul v-for="stock in choosenStock" :key="stock">
              <li @click="popStocks()">{{ stock }}</li>
            </ul>
          </div>
          <br />
          <hr />
          <div id="short-button">
          <label for=""> Show Short and Full Names</label>
          <br />
          <button type="button" class="btn btn-primary" @click="ratesIncr()">
            More
          </button></div>
        </div>
      </div>
      <table>
        <thead>
          <tr>
            <th scope="col">Name</th>
            <th scope="col">Course</th>
            <th scope="col" @click.once="ratesIncr()">ShortName</th>
            <th @click.prevent="ratesIncr()">FullName</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(key, value, index) in rates" :key="index">
            <td @click="addToFauvorite(value)" >
              {{ value }}
            </td>
            <td @click="addToFauvorite(value)">
              {{ key }}
            </td>
            <td v-if="ratesNameShort">
              {{ ratesNameShort[index] }}
            </td>
            <td v-if="ratesNameFull">
              {{ ratesNameFull[index] }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div v-else-if="IError">
        <h1>Please Check your Connection</h1>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      rates: null,
      ratesName: null,
      ratesNameFull: [],
      lastUpdate: null,
      ratesNameShort: [],
      choosenStock: [],
      IError: false
    };
  },
  methods: {
    getRate() {
      if (this.rates) {
        return;
      } else {
        axios
          .get(
            "http://api.currencylayer.com/live?access_key=ba0c1f4bc4e3afe63d553782f9971576"
          )
          .then(
            (response) => (
              (this.rates = response.data.quotes),
              (this.lastUpdate = response.data.timestamp)
            )
          )
          .catch((err) => console.log(err), (this.rates = null),(this.IError = true));
      }
    },
    getRateName() {
      if (this.ratesName) {
        return;
      } else {
        axios
          .get(
            "http://api.currencylayer.com/list?access_key=ba0c1f4bc4e3afe63d553782f9971576"
          )
          .then((response) => (this.ratesName = response.data.currencies))
          .catch((err) => console.log(err), (this.ratesName = null));
      }
    },
    ratesIncr() {
      let i = 0;
      for (let n in this.ratesName) {
        this.ratesNameFull[i] = this.ratesName[n];
        this.ratesNameShort[i] = n;
        i++;
      }
      this.$forceUpdate(this.ratesNameFull, this.ratesNameShort);
      return this.ratesNameFull, this.ratesNameShort;
    },
    addToFauvorite(val) {
      this.choosenStock.push(val);
      console.log(this.choosenStock);
    },
    popStocks(){
        this.choosenStock.pop()
        this.$forceUpdate(this.choosenStock)
    }
  },
  mounted() {
      this.IError = false
    if (!this.rates) {
      this.getRate();
      this.getRateName();
      this.ratesIncr()
    }
  },
  computed: {
    toLocaltime() {
      if (this.lastUpdate) {
        let acc = new Date(this.lastUpdate * 1000);
        return acc.toLocaleString();
      } else {
        return this.toLocaltime();
      }
    },
  },
};
</script>

<style lang="scss" scoped>
#short-button{
    text-align: left;
    margin-left: 3rem;

    button{
        background: #222dcca4;
        color: #f8f88f8f;
        padding: 10px;
        border-radius: 10px;
        margin-bottom: 15px;        
    }
    button:hover{
        background: #0e73d1a4;
        color: whitesmoke
    }
}
table{
    background: #f5f5f2ec;
    color: #353333cb;
}
table, th, td {
  border: 1px solid black;
  font-size: 15px;
}
td{
    width: 26%;
}
</style>