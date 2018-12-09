<template>
  <div class="modal">
    <h2>Add Product</h2>
    <div class="field">
      <label for="">Name:</label>
      <input
        v-model="Product.name"
        type="text"
        placeholder="">
      <!-- <label for="">Category:</label> -->
      <v-select
          placeholder="Category"
          v-model="Product.category"
          label="name"
          :options="categoryOptions">
        </v-select>
      <label for="">Price:</label>
      <input
        v-model="Product.price"
        type="text"
        placeholder="">
      <!-- <label for="">Pricing Unit:</label> -->
      <!-- <div class="unit"> -->
        <v-select
          placeholder="Pricing Unit"
          v-model="Product.pricingUnit"
          label="name"
          :options="unitOptions">
        </v-select>
      <!-- </div> -->
      <!-- <input
        v-model="Product.pricingUnit"
        type="text"
        placeholder=""> -->
      <button class="_button1"
        @click="submit()"
      >
        +
      </button>
    </div>
  </div>
</template>

<script>
import { CREATE_PRODUCTTEMPLATE_MUTATION, ALL_PRODUCTTEMPLATES_QUERY } from '../../../constants/graphql'
import vSelect from 'vue-select'

export default {
  name: 'CreateProduct',
  components: {
    vSelect
  },
  data () {
    return {
      User: {},
      Product: {
        name: '',
        category: '',
        pricingUnit: '',
        price: null
      },
      unitOptions: ['pinch', 'tsp', 'tbsp', 'fl oz', 'cup', 'pt', 'qt', 'gal', 'oz', 'lb'],
      categoryOptions: ['Produce', 'Meat', 'Seafood', 'Dairy', 'Bakery', 'Frozen Food', 'Beverages',
      'Baking Goods', 'Packaged Goods', 'Condiments']
      // currentUserId: this.$store.state.auth.user.id
    }
  },
  methods: {
    submit () {
      // console.log('Shopping List Id', this.$route.params.id)
      let price = parseFloat(this.Product.price)
      this.$apollo.mutate({
        mutation: CREATE_PRODUCTTEMPLATE_MUTATION,
        variables: {
          name: this.Product.name,
          category: this.Product.category,
          pricingUnit: this.Product.pricingUnit,
          price: price,
          template: true
        },
        update: (store, { data: { createProductTemplate } }) => {
          // Pull data from the stored query
          console.log('Store from Products', store)
          const data = store.readQuery({
            query: ALL_PRODUCTTEMPLATES_QUERY
          })
          // We add the new data
          data.allProductTemplates.push(createProductTemplate)
          console.log('Test', data)
          // We update the cache
          store.writeQuery({
            query: ALL_PRODUCTTEMPLATES_QUERY,
            data: data
          })
          this.Product = {}
        }
      }).catch((error) => {
        console.error(error)
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.field {
  margin-top: 2vh;
}
</style>
