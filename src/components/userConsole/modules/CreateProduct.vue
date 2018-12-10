<template>
  <div class="add-product">
    <!-- <h2>Add Product</h2> -->
    <!-- <div class="container"> -->
      <!-- <label for="">Name:</label> -->
      <input
        class="product-name"
        v-model="Product.name"
        type="text"
        placeholder="Name">
      <!-- <label for="">Category:</label> -->
      <v-select
        class="category"
        placeholder="Category"
        v-model="Product.category"
        label="name"
        :options="categoryOptions">
      </v-select>
      <!-- <label for="">Price:</label> -->
      <input
        class="price"
        v-model="Product.price"
        type="text"
        placeholder="Price">
      <!-- <label for="">Pricing Unit:</label> -->
      <!-- <div class="unit"> -->
        <v-select
          class="pricing-unit"
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
    <!-- </div> -->
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
.add-product {
  width: 100%;
  display: grid;
  grid-template-columns: 30% 20% 10% 20% 20%;
  grid-template-areas:
    "product-name category price pricing-unit";
  font-size: 2vmin;
  border: 1px solid lightgray;
  input {
    font-size: inherit;
    padding: 0.8vh 0vw 0.8vh 0.5vw;
  }
  .product-name {
    grid-area: product-name;
    // background-color: pink;
  }
  .category {
    grid-area: category;
    font-size: inherit;
  }
  .price {
    grid-area: price;
  }
  .pricing-unit {
    grid-area: pricing-unit;
  }
}
.field {
  margin-top: 2vh;
}
// ================================================ Mobile Styles ========================================
@media only screen and (max-width: 767px) {
.add-product {
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-areas:
    "product-name  product-name category"
    "price pricing-unit .";
  .product-name {
    border-bottom: 0.15vh solid lightgray;
    border-top: 0vh solid lightgray;
    border-left: 0vh solid lightgray;
    border-right: 0vh solid lightgray;
  }
}
}
</style>
