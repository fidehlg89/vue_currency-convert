<template>
  <div>
    <b-overlay :show="loading" rounded="sm">
    <b-card
      title="Currency convert"
      style="background: #f0f5fa; align-items: center; margin: 5rem auto"
    >
      <b-form inline @submit="onSubmit" @reset="onReset">
        <b-form-input
          id="amount"
          class="mb-2 mr-sm-2 mb-sm-0"
          v-model="form.amount"
          type="number"
          placeholder="Enter amount"
          required
          @input="calcResult"
        ></b-form-input>

        <b-form-select
          class="mb-2 mr-sm-2 mb-sm-0"
          v-model="form.base_currency"
          :options="options"
          @change="setDataFromCurrency"
        ></b-form-select>

        <b-form-select
          class="mb-2 mr-sm-2 mb-sm-0"
          v-model="form.to_currency"
          :options="to_options"
          @change="setCurrencyValue"
          :disabled="is_disabled"
        ></b-form-select>
      </b-form>

      <div class="mt-4">
        <!-- <span v-if="loading == true"
          ><b-spinner
            style="width: 2.25rem; height: 2.25rem"
            variant="primary"
            label="Spinning"
          ></b-spinner
        ></span> -->
        <p class="convert-result">{{ convertResult }}</p>
      </div>
    </b-card>
    </b-overlay>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data: () => ({
    convertResult: 0,
    options: [{ value: "null", text: "Select currency base" }],
    to_options: [{ value: null, text: "Select currency to convert" }],
    form: {
      amount: "",
      base_currency: null,
      to_currency: null,
      currency_value: null,
    },
    loading: false,
    show: false,
  }),
  computed: {
    is_disabled() {
      return this.to_options.length > 1 ? false : true;
    },
  },
  components: {},
  created() {
    this.onInitialize();
  },
  methods: {
    async onInitialize() {
      this.loading = true;
      await this.getData();
      this.loading = false;
      this.show = true;
    },
    async getData() {
      const requestOptions = {
        headers: {
          "Content-Type": "application/json; charset=UTF-8",
          charset: "utf-8",
        },
        params: { apikey: process.env.VUE_APP_API_KEY },
      };

      await axios
        .get("https://freecurrencyapi.net/api/v2/latest", requestOptions)
        .then((res) => {
          var data = res.data.data;
          this.options.push({ value: "USD", text: "USD" });

          for (let currency in data) {
            this.options.push({ value: currency, text: currency });
          }
        })
        .catch((error) => {
          console.error("There was an error!", error);
        });
    },
    async getDataBase(currency_base) {
      const requestOptions = {
        headers: {
          "Content-Type": "application/json; charset=UTF-8",
          charset: "utf-8",
        },
        params: {
          apikey: process.env.VUE_APP_API_KEY,
          base_currency: currency_base,
        },
      };

      await axios
        .get("https://freecurrencyapi.net/api/v2/latest", requestOptions)
        .then((res) => {
          var data = res.data.data;

          this.to_options = [
            { value: null, text: "Select currency to convert" },
          ];

          for (let currency in data) {
            var value = data[currency];
            this.to_options.push({
              value: currency,
              text: currency,
              currency_value: value,
            });
          }

          if (this.form.to_currency != null) {
            this.setCurrencyValue();
          }
        })
        .catch((error) => {
          console.error("There was an error!", error);
        });
    },
    async setDataFromCurrency() {
      this.loading = true;
      var currency_base = this.form.base_currency;
      await this.getDataBase(currency_base);
      this.calcResult();
      this.loading = false;
    },
    setCurrencyValue() {
      var currencies = this.to_options;
      var result = currencies.filter(
        (currency) => currency.value === this.form.to_currency
      );
      this.form.currency_value = result[0].currency_value;
      this.calcResult();
    },
    onSubmit(event) {
      event.preventDefault();
      this.calcResult();
    },
    calcResult() {
      this.loading = true;
      var amount = this.form.amount;

      if (amount == 0 || amount == null || amount == "") {
        amount=1;
      }
      this.convertResult = Number(amount * this.form.currency_value);

      this.loading = false;
    },
    onReset(event) {
      event.preventDefault();
      // Reset our form values
      this.form.email = "";
      this.form.name = "";
      this.form.food = null;
      this.form.checked = [];
      // Trick to reset/clear native browser form validation state
      this.show = false;
      this.$nextTick(() => {
        this.show = true;
      });
    },
  },
};
</script>

<style>
.convert-result {
  font-size: 3rem;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
