<template>
  <v-layout wrap>
    <v-flex xs12 pa-3>
      <products-amount-vue :products="sortedProducts" />
    </v-flex>

    <v-flex xs12>
      <v-list xs12 two-line>
        <v-list-item>
          <v-layout
            wrap
            justify-start
            align-center
            :class="{'mobileColumn':$vuetify.breakpoint.smAndDown}"
          >
            <v-flex xs1>
              <v-list-item-content>
                <strong>單</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs4 sm3 :class="{'sm4': !$store.getters.isAdmin}">
              <v-list-item-content>
                <strong>商品</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs2 sm1>
              <v-list-item-content>
                <strong>量</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1 v-if="$vuetify.breakpoint.smAndUp  && $store.getters.isAdmin">
              <v-list-item-content>
                <strong>買(AU)</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs2 sm1 :class="{'sm2': !$store.getters.isAdmin}">
              <v-list-item-content>
                <strong>賣(TW)</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1 v-if="$vuetify.breakpoint.smAndUp">
              <v-list-item-content>
                <strong>廠商</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1 v-if="$vuetify.breakpoint.smAndUp  && $store.getters.isAdmin">
              <v-list-item-content>
                <strong>利(TW)</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1>
              <v-list-item-content>
                <strong>買</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1>
              <v-list-item-content>
                <strong>付</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1>
              <v-list-item-content>
                <strong>收</strong>
              </v-list-item-content>
            </v-flex>
          </v-layout>
        </v-list-item>
        <v-divider></v-divider>
        <v-list-item v-for="(p, idx) in sortedProducts" :key="idx">
          <v-layout wrap justify-start align-center>
            <v-flex xs1>
              <v-list-item-content>
                <strong>{{p.orderNum}}</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs4 sm3 :class="{'sm4': !$store.getters.isAdmin}">
              <v-list-item-content :class="{'mobileListFont':$vuetify.breakpoint.smAndDown}">
                <nuxt-link :to="`/member/${member._id}/product/${p._id}`">
                  <strong>{{p.name}}</strong>
                </nuxt-link>
              </v-list-item-content>
            </v-flex>
            <v-flex xs2 sm1>
              <v-list-item-content>
                <strong>{{p.quantity}}</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1 v-if="$vuetify.breakpoint.smAndUp  && $store.getters.isAdmin">
              <v-list-item-content>
                <strong>{{p.buyPriceAUD}}</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs2 sm1 :class="{'sm2': !$store.getters.isAdmin}">
              <v-list-item-content>
                <strong>{{p.sellPriceTWD}}</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1 v-if="$vuetify.breakpoint.smAndUp">
              <v-list-item-content>
                <strong>{{p.seller}}</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1 v-if="$vuetify.breakpoint.smAndUp  && $store.getters.isAdmin">
              <v-list-item-content>
                <strong>{{((p.sellPriceTWD - p.buyPriceAUD * 22)*p.quantity).toFixed(2)}}</strong>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1>
              <v-list-item-content>
                <v-icon
                  @click="()=>toggleBox('bought', p._id, p.bought)"
                >{{p.bought? "check_box":"check_box_outline_blank"}}</v-icon>
              </v-list-item-content>
            </v-flex>
            <v-flex xs1>
              <v-list-item-content>
                <v-icon
                  @click="()=>toggleBox('paid', p._id, p.paid)"
                >{{p.paid? "check_box":"check_box_outline_blank"}}</v-icon>
              </v-list-item-content>
            </v-flex>

            <v-flex xs1>
              <v-list-item-content>
                <v-icon
                  @click="()=>toggleBox('received', p._id, p.received)"
                >{{p.received? "check_box":"check_box_outline_blank"}}</v-icon>
              </v-list-item-content>
            </v-flex>
          </v-layout>
        </v-list-item>
        <v-divider></v-divider>
      </v-list>
    </v-flex>
  </v-layout>
</template>

<script>
import ProductsAmountVue from "./ProductsAmount.vue";

export default {
  props: ["member"],

  computed: {
    sortedProducts() {
      return this.member.products.concat().sort((a, b) => {
        return Number(b.orderNum) - Number(a.orderNum);
      });
    }
  },

  components: { ProductsAmountVue },

  methods: {
    async toggleBox(type, pid, currentState) {
      switch (type) {
        case "paid":
          await this.$store.dispatch("updateProductToMember", {
            uid: this.$route.params.uid,
            pid,
            product: { paid: !currentState }
          });
          break;
        case "bought":
          await this.$store.dispatch("updateProductToMember", {
            uid: this.$route.params.uid,
            pid,
            product: { bought: !currentState }
          });
          break;
        case "received":
          await this.$store.dispatch("updateProductToMember", {
            uid: this.$route.params.uid,
            pid,
            product: { received: !currentState }
          });
          break;
        default:
          console.log("Not supported");
      }
    }
  }
};
</script>

<style>
</style>
