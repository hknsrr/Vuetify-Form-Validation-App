<template>
  <div id="mortgage-form">
    <v-app>
      <v-form v-model="valid" ref="form" lazy-validation>
        <v-container fluid grid-list-xs>
          <v-layout row>
            <v-flex xs6>
              <v-text-field
                label="Name"
                v-model="name"
                :rules="nameRules"
                :counter="10"
                required
              ></v-text-field>
            </v-flex>
            <v-flex xs6>
              <v-text-field
                label="Last Name"
                v-model="lastname"
                :rules="nameRules"
                :counter="10"
                required
              ></v-text-field>
            </v-flex>
          </v-layout>
          <v-layout row>
            <v-flex xs12>
              <v-text-field
                label="TCKN"
                v-model="tckn"
                :rules="tcknRules"
                :counter="11"
                mask="###########"
                type="tel"
                required
              ></v-text-field>
            </v-flex>
          </v-layout>
          <v-layout row>
            <v-flex xs6>
              <v-text-field
                label="House Value"
                v-model="house"
                :rules="amountRules"
                mask="#### ###"
                type="tel"
                suffix="TL"
                required
              ></v-text-field>
            </v-flex>
            <v-flex xs6>
              <v-text-field
                label="Loan Amount"
                v-model="loan"
                :rules="amountRules"
                mask="#### ###"
                type="tel"
                suffix="TL"
                required
              ></v-text-field>
            </v-flex>
          </v-layout>
                  <v-alert type="warning" :value="!calcLoanAmount">
                The loan amount should be less than %75 of the value of the house.
              </v-alert>
          <v-layout row>
            <v-flex xs12 maturity>
                <label>Maturity (Months)*</label>
                <v-badge color="blue">
                  <span slot="badge">{{maturity}}</span>
                  <v-icon medium color="grey">alarm</v-icon>
                </v-badge>
              <v-slider v-model="maturity" thumb-label step="2" min="12" max="120" ticks></v-slider>
            </v-flex>
          </v-layout>
          <v-layout row justify-center>
            <v-flex xs3>
              <v-btn
                @click="submit"
                :disabled="!valid"
              >
              Search
              </v-btn>
            </v-flex>
            <v-flex xs3>
              <v-btn @click="clear">clear</v-btn>
            </v-flex>
          </v-layout>
        </v-container>
      </v-form>
    </v-app>
  </div>
</template>

<script>
export default {
  name: "MortgageForm",
  data: () => ({
    valid: true,
    name: "",
    lastname: "",
    tckn: "",
    house: 2000,
    loan: 1500,
    maturity: 12,
    url: "",
    possibleValues: [12, 18, 24, 36, 48, 60, 72, 90, 108, 120],
    nameRules: [
      v => !!v || "This is required",
      v => (v && v.length <= 10) || "This must be less than 10 characters",
      v => (v && v.length >= 2) || "This must be more than 2 characters",
      v => !/\d/.test(v) || "Allows only letters not numbers"
    ],
    tcknRules: [
      v => !!v || "TCKN is required",
      v => !(v && v.length < 11) || "TCKN must be 11 characters",
      v => (/\d/.test(v) && !isNaN(v)) || "Allows only numbers not letters"
    ],
    amountRules: [
      v => !!v || "This is required",
      v =>
        (v && v[0] != 0 && v >= 1000 && v <= 1000000) ||
        "This must be between 1.000 TL and 1.000.000 TL",
      v => (/\d/.test(v) && !isNaN(v)) || "Allows only numbers not letters"
    ]
  }),
  computed: {
    calcLoanAmount() {
      return this.loan <= this.house * 75 / 100 ? true : false;
    }
  },
  watch: {
    maturity: function(val) {
      var closest = this.possibleValues.reduce(function(prev, curr) {
        return Math.abs(curr - val) < Math.abs(prev - val) ? curr : prev;
      });
      this.maturity = closest;  
    }
  },
  methods: {
    submit() {
      if (this.$refs.form.validate()) {
        // www.hesapkurdu.com/konut-kredisi?r=[value-of-house]&l=[loanamount]&m=[maturity]&n=[name]&s=[last name] &t=[tckn]
        this.url =
          "https://www.hesapkurdu.com/konut-kredisi?r=" +
          this.house +
          "&l=" +
          this.loan +
          "&m=" +
          this.maturity +
          "&n=" +
          this.name +
          "&s=" +
          this.lastname +
          "&t=" +
          this.tckn;
        window.open(this.url, "_blank");
      }
    },
    clear() {
      this.$refs.form.reset();
      this.house = 1000;
      this.loan = 750;
      this.maturity = 12;
    }
  }
};
</script>