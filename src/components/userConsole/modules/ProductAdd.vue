<template>
  <div class="product-add">
      <v-select
        class="product-name"
        placeholder="Add Product"
        v-model="selected"
        label="name"
        :options="query">
      </v-select>
      <input
        class="qty"
        v-model="quantity"
        type="text"
        placeholder="Qty">
      <v-select
        class="unit"
        placeholder="Unit"
        v-model="selectedUnit"
        label="name"
        :options="units">
      </v-select>
      <input
        class="format"
        v-model="format"
        type="text"
        placeholder="Chopped, sliced, minced, etc.">
    <button class="add-button _button1"
      @click="submit()"
    >
      +
    </button>
  </div>
</template>

<script>
import { CREATE_PRODUCT_MUTATION, ALL_PRODUCTTEMPLATES_QUERY, MY_PRODUCTS_QUERY } from '../../../constants/graphql'
import vSelect from 'vue-select'

export default {
  name: 'SelectProduct',
  components: {
    vSelect
  },
  data () {
    return {
      query: [{label: 'Cookies', value: 'Cookies'}, {label: 'Broccoli', value: 'Broccoli'}],
      units: [
        {name: 'pinch', value: 'pinch'},
        {name: 'tsp', value: 'tsp'},
        {name: 'tbsp', value: 'tbsp'},
        {name: 'fl oz', value: 'fl oz'},
        {name: 'cup', value: 'cup'},
        {name: 'pt', value: 'pt'},
        {name: 'qt', value: 'qt'},
        {name: 'gal', value: 'gal'},
        {name: 'oz', value: 'oz'},
        {name: 'lb', value: 'lb'}
      ],
      User: {},
      name: '',
      category: '',
      selectedUnit: '',
      format: '',
      price: null,
      selected: '',
      quantity: ''

      // currentUserId: this.$store.state.auth.user.id
    }
  },
  apollo: {
    allProductTemplates: {
      query: ALL_PRODUCTTEMPLATES_QUERY,
      result ({ data }) {
        // Sets variable query to the gql data for a more modular UI template
        this.query = data.allProductTemplates
        // this.isEditMode = data.isEditMode
      }
    },
    allProducts: {
      query: MY_PRODUCTS_QUERY,
      variables () {
        return {
          listId: this.$route.params.id
        }
      },
      result ({ data }) {
        // Sets variable query to the gql data for a more modular UI template
        // this.query = data.allProductTemplates
        // this.isEditMode = data.isEditMode
      }
    }
  },
  methods: {
    submit () {
      console.log('Selected', this.selected)
      console.log('Shopping List Id', this.$route.params.id)
      let quantity = parseFloat(this.quantity)
      // let price = parseFloat(this.price)
      this.$apollo.mutate({
        mutation: CREATE_PRODUCT_MUTATION,
        variables: {
          templateId: this.selected.id,
          shoppingListId: this.$route.params.id,
          quantity: quantity,
          format: this.format,
          unit: this.selectedUnit.value
        },
        update: (store, { data: { createProduct } }) => {
          // Pull data from the stored query
          const data = store.readQuery({
            query: MY_PRODUCTS_QUERY,
            variables: {
              listId: this.$route.params.id
            }
          })
          // We add the new data
          data.allProducts.push(createProduct)
          console.log('Test', data)
          // We update the cache
          store.writeQuery({
            query: MY_PRODUCTS_QUERY,
            variables: {
              listId: this.$route.params.id
            },
            data: data
          })
        }
      }).catch((error) => {
        console.error(error)
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.product-add {
  width: 100%;
  display: grid;
  grid-template-columns: 30% 10% 20% 30% 10%;
  grid-template-areas:
    "product-name qty unit format";
  font-size: 2vmin;
  border: 1px solid lightgray;
  input {
    font-size: inherit;
    padding: 1vh 0vw 1vh 0.5vw;
  }
  .product-name {
    grid-area: product-name;
    font-size: 2vmin;
    // background-color: pink;
  }
  .qty {
    grid-area: qty;
  }
  .unit {
    grid-area: unit;
  }
  .format {
    grid-area: format;
  }
}

// ================================================ Mobile Styles ========================================
@media only screen and (max-width: 767px) {
  .product-add {
    grid-template-columns: 30% 50% 20%;
    grid-template-areas:
      "product-name product-name qty"
      "unit format .";
    .qty {
      border-bottom: 0.15vh solid lightgray;
      border-top: none;
      border-left: none;
      border-right: none;
      height: 2vh;
    }
    // .product-name {
    //   height: 3.5vh;
    // }
  }
}
</style>
