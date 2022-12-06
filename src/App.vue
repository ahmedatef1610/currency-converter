<template>
  <div class="my-5">
    <div class="container">
      <!------------>
      <div class="row">
        <div class="col-md-6">
          <div class="form-group">
            <label for="from">From</label>
            <select class="form-control" id="from" v-model="from">
              <option
                v-for="(currency,i)  in formattedCurrncies"
                :value="currency.id"
                :key="i"
              >{{currency.currencyName}} ({{currency.currencySymbol}})</option>
            </select>
          </div>
        </div>

        <div class="col-md-6">
          <div class="form-group">
            <label for="to">To</label>
            <select class="form-control" id="to" v-model="to">
              <option
                v-for="(currency,i)  in formattedCurrncies"
                :value="currency.id"
                :key="i"
              >{{currency.currencyName}} ({{currency.currencySymbol}})</option>
            </select>
          </div>
        </div>
      </div>
      <!------------>
      <div class="row">
        <div class="col-md-6 mx-auto mt-5">
          <div class="form-group">
            <label for="amount">Amount</label>
            <input
              type="number"
              class="form-control"
              id="amount"
              placeholder="Amount"
              v-model.number="amount"
            >
          </div>
          <div class="text-center">
            <button
              :disabled="disabled"
              class="btn btn-primary btn-lg"
              @click="convertCurrencies()"
            >{{loading?'Converting ....':'Convert'}}</button>
          </div>
        </div>
      </div>
      <!------------>
      <div class="row">
        <div class="col-md-6 mx-auto mt-5">
          <div class="display-4 text-center">{{calculateResult}}</div>
        </div>
      </div>
      <!------------>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      currencies: {},
      from: "USD",
      to: "EUR",
      amount: 0,
      result: 0,
      loading: false
    };
  },
  mounted() {
    this.getCurrencies();
  },
  methods: {
    getCurrencies() {
      this.currencies = JSON.parse(localStorage.getItem("currencies") || "[]");
      axios
        .get(`https://free.currconv.com/api/v7/currencies?apiKey=840b0aa7561b389da785`)
        .then(res => {
          this.currencies = res.data.results;
          localStorage.setItem("currencies", JSON.stringify(res.data.results));
        });
    },
    convertCurrencies() {
      let key = `${this.from}_${this.to}`;
      this.loading = true;
      axios
        .get(
          `https://free.currconv.com/api/v7/convert?q=${key}&compact=ultra&apiKey=840b0aa7561b389da785`
        )
        .then(res => {
          this.loading = false;
          //console.log(res.data[key]);
          this.result = res.data[key];
        });
    }
  },
  computed: {
    formattedCurrncies() {
      return Object.values(this.currencies);
    },
    calculateResult() {
      return (Number(this.amount) * this.result).toFixed(5);
    },
    disabled() {
      return !this.amount || this.amount <= 0 || this.loading;
    }
  },
  watch: {
    from() {
      this.result = 0;
    },
    to() {
      this.result = 0;
    },
    amount(value) {
      if (value < 0) {
        this.amount = 0;
      }
    }
  }
};
</script>

<style lang="scss">
</style>
