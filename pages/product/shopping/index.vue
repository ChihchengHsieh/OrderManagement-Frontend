<template>
  <v-layout wrap>
    <v-flex xs8 sm10>
      <v-text-field label="搜尋" v-model="searching"></v-text-field>
    </v-flex>
    <v-flex xs4 sm2 pa-3>
      <v-btn @click.stop="cleanInput" large color="primary">清除</v-btn>
    </v-flex>

    <v-flex xs12 pa-2>
      <products-amount-vue :products="buyingProduct" />
    </v-flex>

    <v-flex xs12>
      <div
        v-infinite-scroll="loadMoreProdcuts"
        infinite-scroll-disabled="busy"
        infinite-scroll-distance="10"
        infinite-scroll-throttle-delay="10000"
      >
        <product-list-vue @copyToSearch="addSearch" :products="showingProducts"></product-list-vue>
      </div>
    </v-flex>
    <v-flex xs5></v-flex>
    <v-flex xs2 class="mt-2">
      <v-btn disabled color="primary">{{ busy ? "End": "Loading..."}}</v-btn>
    </v-flex>
    <v-flex xs5></v-flex>
  </v-layout>
</template>

<script>
import ProductListVue from "../../../components/ProductList.vue";
import ProductsAmountVue from "../../../components/ProductsAmount.vue";
export default {
  data() {
    return {
      searching: "",
      pageN: 1,
      pageSize: 20,
      busy: false
    };
  },

  components: {
    ProductListVue,
    ProductsAmountVue
  },

  computed: {
    loadMoreProdcuts() {
      // console.log("Loading Called");
      this.pageN++;
    },
    buyingProduct() {
      if (this.searching.trim() === "") {
        return this.$store.getters.allProducts.filter(p => !p.bought);
      }
      return this.$store.getters.allProducts.filter(
        p => p.name.includes(this.searching) && !p.bought
      );
    },
    sortedProducts() {
      return this.buyingProduct.concat().sort((a, b) => {
        return Number(b.orderNum) - Number(a.orderNum);
      });
    },
    showingProducts() {
      if (this.sortedProducts.length > this.pageN * this.pageSize) {
        this.busy = false;
        return this.sortedProducts.slice(0, this.pageN * this.pageSize);
      }
      this.busy = true;
      return this.sortedProducts;
    }
  },
  methods: {
    addSearch(name) {
      // console.log("AddSearch called");
      this.searching = name;
    },
    cleanInput() {
      this.searching = "";
    }
  }
};
</script>

<style>
</style>
