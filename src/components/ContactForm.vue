<template>
  <v-form ref="form" v-model="valid" lazy-validation>
    <v-card>
      <v-card-text>
        <v-text-field
          v-model="name"
          :rules="nameRules"
          label="Name"
          required
          clearable
        ></v-text-field>
        <v-text-field
          v-model="lastname"
          :rules="nameRules"
          label="Last Name"
          required
          clearable
        ></v-text-field>
        <v-text-field
          prepend-icon="mdi-email"
          v-model="email"
          :rules="emailRules"
          label="Contact e-mail"
          required
          clearable
        ></v-text-field>
        <v-autocomplete
          v-model="country"
          :items="countries"
          item-text="name"
          item-value="alpha2Code"
          :rules="[(v) => !!v || 'field is required']"
          label="Purpose of the trip"
          return-object
          required
          append-icon="mdi-airplane-search"
          hide-no-data
          :loading="loading"
          clearable
        ></v-autocomplete>

        <!-- Bagin of informations about the selected country -->
        <v-card v-if="country" class="center-horizontally">
          <v-row>
            <v-col>
              <div class="title-wrapper">
                <v-icon role="img" aria-hidden="false"> mdi-city </v-icon>
                <div>
                  <v-card-title>{{ country.capital }}</v-card-title>
                  <v-card-subtitle>Population</v-card-subtitle>
                </div>
              </div>
              <div class="title-wrapper">
                <v-icon role="img" aria-hidden="false">
                  mdi-account-multiple
                </v-icon>
                <div>
                  <v-card-title>{{ country.population }}</v-card-title>
                  <v-card-subtitle>Population</v-card-subtitle>
                </div>
              </div>
            </v-col>
            <v-col>
              <div class="title-wrapper">
                <v-icon role="img" aria-hidden="false"> mdi-earth </v-icon>
                <div>
                  <v-card-title>{{ country.language[0] }}</v-card-title>
                  <v-card-subtitle>Language</v-card-subtitle>
                </div>
              </div>
              <div class="title-wrapper">
                <v-icon role="img" aria-hidden="false"> mdi-search-web </v-icon>
                <div>
                  <v-card-title>{{ country.region }}</v-card-title>
                  <v-card-subtitle>Region</v-card-subtitle>
                </div>
              </div>
            </v-col>
          </v-row>
        </v-card>
        <!-- End of informations about the selected country -->

        <v-checkbox
          v-model="checkbox"
          :rules="[(v) => !!v || 'You must agree to continue!']"
          label="I agree to the terms and conditions"
          required
          hide-details
        ></v-checkbox>
      </v-card-text>
      <v-card-actions>
        <v-btn :disabled="!valid" color="primary" @click="send" block>
          Send an inquiry
        </v-btn>
      </v-card-actions>
      <v-divider></v-divider>
      <v-btn color="info" small text @click="reset" block> reset </v-btn>
    </v-card>
  </v-form>
</template>

<script>
import { mdiCity, mdiEarth, mdiAccountMultiple, mdiSearchWeb } from "@mdi/js";
export default {
  name: "ContactForm",
  data: () => ({
    loading: false,
    valid: false,
    name: "",
    lastname: "",
    nameRules: [
      (v) => !!v || "Name is required",
      (v) => (v && v.length >= 3) || "Name must be more than 2 characters",
    ],
    email: "",
    emailRules: [
      (v) => !!v || "E-mail is required",
      (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
    ],
    country: null, //selected country
    countries: [], //list of available countries
    checkbox: false,
    icons: {
      mdiCity,
      mdiEarth,
      mdiAccountMultiple,
      mdiSearchWeb,
    },
  }),
  async created() {
    this.loading = true;
    this.countries = await this.getCountries();
    this.loading = false;
  },
  methods: {
    async getCountries() {
      try {
        let page = 1;
        let allCountries = [];
        while (page <= 100) {
          // limit max number of iterations
          const response = await fetch(
            `https://jsonmock.hackerrank.com/api/countries?page=${page}`
          );
          const data = await response.json();
          allCountries = [...allCountries, ...data.data];
          this.countries = allCountries;
          if (data.page == data.total_pages) break;
          page++;
        }
      } catch (error) {
        console.error(error);
      }
    },
    send() {
      this.valid = this.validation();
      if (this.valid) alert("The application has been sent!");
    },
    reset() {
      this.$refs.form.reset();
    },
    validation() {
      return this.$refs.form.validate();
    },
    showInformation() {
      console.log("boo");
    },
  },
  mounted() {
    this.getCountries();
  },
};
</script>

<style scoped>
.center-horizontally {
  display: flex;
  justify-content: center;
  align-items: center;
}
.title-wrapper {
  display: flex;
  align-items: center;
  margin-left: 30px;
}

.title-wrapper v-icon {
  margin-right: 10px;
}
</style>
