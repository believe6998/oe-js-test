<template>
  <q-page class="q-pa-lg">
    <div class="row justify-between">
      <div class="col-5 col-sm-4 col-md-2">
        <q-btn color="indigo-10"
               label="Filter"
               no-caps
               size="lg"
               style="width: 100%"
               @click="doFilter"/>
      </div>
      <div class="col-5 col-sm-4 col-md-2">
        <q-select outlined
                  bg-color="grey-4"
                  color="indigo-10"
                  label="Sort by"
                  label-color="indigo-10"
                  v-model="sort"
                  :options="options"
                  emit-value
                  map-options/>
      </div>
    </div>
    <q-card bordered class="q-mt-lg">
      <q-card-section class="q-pb-none text-indigo-10">
        <div class="text-h6">Toppings:</div>
      </q-card-section>

      <q-card-section class="q-pt-none">
        <div class="row q-col-gutter-lg">
          <div class="col-12 col-sm-6 col-md-3"
               v-for="topping in toppings"
               :key="topping.id">
            <q-checkbox v-model="filter"
                        size="xl"
                        :label="topping.name"
                        :val="topping.id"
                        color="indigo-10"/>
          </div>
        </div>
      </q-card-section>
    </q-card>

    <div class="row q-pt-lg q-col-gutter-md">
      <div class="col-12 col-sm-6 col-md-4 col-lg-3"
           v-for="product in myProducts"
           :key="product.id">
        <q-card class="text-indigo-10">
          <q-card-section class="text-h6">
            <div class="text-weight-light" v-if="product.id < 10">MT-0{{ product.id }}</div>
            <div class="text-weight-light" v-else>MT-{{ product.id }}</div>
            <div>{{ product.name }}</div>
          </q-card-section>

          <q-separator inset="" color="indigo-10" size="3px"/>

          <q-card-section>
            <div class="text-bold text-body1">Toppings:</div>
            <div class="text-body2">{{ loadToppings(product.id) }}</div>
          </q-card-section>

          <q-card-section>
            <div class="row justify-end">
              <div class="text-h6">${{ product.price }}</div>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script>
import {toppings} from '../data/toppings.json'
import {toppingProducts} from '../data/toppingProducts.json'

export default {
  props: ['products'],
  data() {
    return {
      filter: [],
      sort: '',
      toppings: toppings,
      myProducts: [],
      options: [
        {
          label: 'Name (Asc)',
          value: 'nameAsc'
        },
        {
          label: 'Name (Dsc)',
          value: 'nameDsc'
        },
        {
          label: 'Price (Asc)',
          value: 'priceAsc'
        },
        {
          label: 'Price (Dsc)',
          value: 'priceDsc'
        }
      ]
    }
  },
  watch: {
    products(val){
      this.filter = []
      this.myProducts = val
    },
    sort(val) {
      switch (val) {
        case 'nameAsc':
          this.myProducts.sort((a, b) => a.name.length - b.name.length)
          break;
        case 'nameDsc':
          this.myProducts.sort((a, b) => b.name.length - a.name.length)
          break;
        case 'priceAsc':
          this.myProducts.sort((a, b) => parseFloat(a.price) - parseFloat(b.price))
          break;
        case 'priceDsc':
          this.myProducts.sort((a, b) => parseFloat(b.price) - parseFloat(a.price))
          break;
      }
    }
  },
  methods: {
    loadToppings(productId){
      let productToppings = toppingProducts.filter(x => x.product === productId)
      let toppingIds = productToppings.map(x => x.topping)
      let toppingNames= []
      for (let topping of toppings) {
        let isExist = toppingIds.includes(topping.id)
        if (isExist === true) {
          toppingNames.push(topping.name)
        }
      }
      return toppingNames.join(', ')
    },
    doFilter() {
      if (this.filter.length > 0){
        let filterProductIds = []
        let filterProduct = []
        for (let toppingProduct of toppingProducts) {
          let isExist = this.filter.includes(toppingProduct.topping)
          if (isExist === true) {
            filterProductIds.push(toppingProduct.product)
          }
        }
        for (let product of this.products){
          let isExist = filterProductIds.includes(product.id)
          if (isExist === true) {
            filterProduct.push(product)
          }
        }
        this.myProducts = filterProduct
      }else {
        this.myProducts = this.products
      }
    }
  }
}
</script>
