<template>
  <q-layout view="lHh LpR lFf">
    <q-header reveal>
      <q-toolbar class="bg-indigo-10">
        <q-btn @click="left = !left" flat round dense icon="menu" class="q-mr-sm"/>
        <q-toolbar-title>{{ storeSelected.name }}</q-toolbar-title>
      </q-toolbar>
    </q-header>
    <q-drawer content-class="bg-indigo-9 text-white"
              show-if-above
              v-model="left"
              side="left"
              elevated>
      <div class="full-height">
        <div style="height: calc(100% - 117px);padding:10px;">
          <q-toolbar>
            <q-toolbar-title class="text-center">Milk Tea Store</q-toolbar-title>
          </q-toolbar>
          <hr/>
          <q-scroll-area style="height:100%;">
            <q-list padding>
              <q-item exact
                      class="q-ma-sm navigation-item"
                      clickable
                      v-ripple
                      v-for="store in stores"
                      :key="store.id"
                      :class="{ 'bg-indigo-6' : storeSelected.id === store.id }"
                      @click="loadProducts(store)">
                <q-item-section class="text-center">
                  <q-item-label class="text-h6"
                                :class="{ 'text-grey' : storeSelected.id !== store.id}">
                    {{ store.name }}
                  </q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-scroll-area>
        </div>
      </div>
    </q-drawer>

    <q-page-container>
      <q-page class="row no-wrap">
        <div class="col">
          <div class="full-height">
            <q-scroll-area class="col q-pr-sm full-height" visible>
              <index :products="products"></index>
            </q-scroll-area>
          </div>
        </div>
      </q-page>
    </q-page-container>


  </q-layout>
</template>

<script>
import {stores} from '../data/stores.json'
import {shopProducts} from '../data/storeProducts.json'
import {products} from "src/data/products.json";
import Index from '../pages/Index'
import {toppingProducts} from "src/data/toppingProducts.json";
import {toppings} from "src/data/toppings.json";

export default {
  components: {
    Index
  },
  data() {
    return {
      left: false,
      stores: stores,
      storeSelected: {},
      products: []
    }
  },
  methods: {
    loadProducts(store) {
      this.storeSelected = store
      let storeProductsSelected = shopProducts.filter(x => x.shop === this.storeSelected.id)
      let productIds = storeProductsSelected.map(x => x.product)
      this.products = []
      for (let product of products) {
        let isExistProduct = productIds.includes(product.id)
        if (isExistProduct === true) {
          this.products.push(product)
        }
      }
    }
  },
  mounted() {
    this.loadProducts(this.stores[0])
  }
}
</script>

<style scoped>
.navigation-item {
  border-radius: 5px;
}

body {
  background: #f1f1f1 !important;
}

.store-selected {
  background: royalblue;
}
</style>
