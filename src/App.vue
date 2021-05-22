<template>
  <v-app class="wrapper">
    <v-content>
      <v-row align="center" class="px-3">
        <v-col cols="12" sm="12">
          <v-text-field
            v-model="search"
            label="Search furniture"
          ></v-text-field>
        </v-col>
        <v-col cols="12" sm="6">
          <v-combobox
            v-model="selectedFurnitures"
            :items="furnitures"
            chips
            clearable
            label="Furniture style"
            multiple
          ></v-combobox>
        </v-col>
        <v-col cols="12" sm="6">
          <v-combobox
            v-model="selectedDelivery"
            :items="delivery"
            item-text="day"
            item-value="value"
            label="Delivery time"
            chips
            multiple
          ></v-combobox>
        </v-col>
      </v-row>
      <v-container fluid grid-list-lg>
        <v-layout row wrap>
          <v-flex sm6 v-if="products.length == 0">
            <v-skeleton-loader
              class="mx-auto"
              type="article"
            ></v-skeleton-loader>
          </v-flex>

          <v-flex
            else
            sm6
            v-for="(product, index) in filteredItems"
            :key="index"
          >
            <v-card class="mx-auto pa-3">
              <v-card-text>
                <div class="d-flex justify-space-between align-center">
                  <h1 class="headline font-weight-bold">{{ product.name }}</h1>
                  <p
                    class="subtitle-1 font-weight-bold orange--text text--darken-1"
                  >
                    {{ product.price | currency }}
                  </p>
                </div>

                <div class="text--primary">
                  {{ product.description | truncate(114, "...") }}
                </div>

                <div class="mt-5 d-flex justify-space-between align-center">
                  <div class="chips">
                    <v-chip
                      small
                      color="indigo"
                      dark
                      v-for="(furniture, index) in product.furniture_style"
                      :key="index"
                      >{{ furniture }}</v-chip
                    >
                  </div>

                  <p class="font-weight-bold indigo--text text--darken-1">
                    {{ product.delivery_time }} days
                  </p>
                </div>
              </v-card-text>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data: () => ({
    products: [],
    search: "",
    furnitures: [],
    selectedFurnitures: [],
    delivery: [
      {
        day: "1 week",
        value: "7",
      },
      {
        day: "2 week",
        value: "14",
      },
      {
        day: "1 month",
        value: "30",
      },
      {
        day: "More",
        value: "60",
      },
    ],
    selectedDelivery: [],
  }),
  mounted() {
    this.load();
  },
  methods: {
    load() {
      axios
        .get("http://www.mocky.io/v2/5c9105cb330000112b649af8")
        .then((response) => {
          this.products = response.data.products;
          this.furnitures = response.data.furniture_styles;
          console.log(this.products);
          // console.log(this.furnitures);
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  filters: {
    truncate: function (text, length, suffix) {
      return text.substring(0, length) + suffix;
    },
    currency: function (value) {
      if (typeof value !== "number") {
        return value;
      }
      var formatter = new Intl.NumberFormat("id-ID", {
        style: "currency",
        currency: "IDR",
        minimumFractionDigits: 0,
      });
      return formatter.format(value);
    },
  },
  computed: {
    filteredItems: function () {
      var self = this;
      var result = this.products.filter(function (product) {
        return (
          product.name.toLowerCase().indexOf(self.search.toLowerCase()) >= 0
        );
      });

      if (this.selectedFurnitures.length > 0) {
        result = this.products.filter((product) =>
          this.selectedFurnitures.every(
            (val) => product.furniture_style.indexOf(val) >= 0
          )
        );
      }

      // if(this.selectedDelivery.length > 0) {
      //   result = this.products.filter()
      // }

      return result;
    },
  },
};
</script>

<style>
h1,
h2,
h3,
h4,
h5,
h6 {
  color: #111;
}

body {
  background-color: #eee;
}

.wrapper {
  margin: 5rem auto;
  max-width: 60rem;
  background: transparent !important;
}

@media (max-width: 60rem) {
  .wrapper {
    margin: 1rem auto;
  }
}

.chips > * + * {
  margin-left: 0.65rem;
}
</style>
