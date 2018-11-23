<template>
  <div class="container">
    <!-- <div class="product"> -->
      <v-select
        class="product"
        placeholder="Add Product"
        v-model="selected"
        label="name"
        :options="query">
      </v-select>
    <!-- </div> -->
    <div class="qty">
      <input
        v-model="quantity"
        type="text"
        placeholder="Qty">
    </div>
    <div class="unit">
      <v-select
        placeholder="Unit"
        v-model="selectedUnit"
        label="name"
        :options="units">
      </v-select>
    </div>
    <div class="format">
      <input
        v-model="format"
        type="text"
        placeholder="Chopped, sliced, minced, etc.">
    </div>
    <!-- <label for="">Quantity:</label> -->
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
@media only screen and (min-width: 768px) {
    /* tablets and desktop */
}

/* phones */
@media only screen and (max-width: 767px) {
  .container {
    width: auto;
    margin: 0vh 5vw;
    display: grid;
    grid-template-rows: auto auto auto;
    grid-template-columns: auto auto;
    grid-template-areas:
      "product product"
      "qty unit"
      "format add-button";
    .product {
      width: 89vw;
      grid-area: product;
      // border: 1px solid red;
    }
    .qty {
      grid-area: qty;
      width: 30vw;
      margin-right: 5vw;
      border-bottom: 0.15vh solid lightgray;
    }
    .unit {
      grid-area: unit;
      width: 58vw;
      margin-left: 4.5vw;
      // border: 1px solid blue;
    }
    .format {
      grid-area: format;
      width: 78vw;
      // margin-right: 10vw;
      // border: 1px solid red;
    }
    .add-button {
      grid-area: add-button;
      width: 10vw;
      margin-left: 52.5vw;
    }
  }
}

@media only screen and (max-width: 767px) and (orientation: portrait) {
    /* portrait phones */
}
.container {
  border: 1px solid lightgray;
  display: grid;
  // width: 100%;
  margin: 0px;
  padding: 0px;
  grid-template-columns: 30% 10% 10% 35% 15%;
  font-size: 3vh;
  .product {
    font-size: inherit;
    // width: 20vw;
    // font-size: 3vh;
  }
  .qty {
    input {
      font-size: inherit;
      padding: 0.9vh 0vw 0.9vh 0vw;
      width: 100%;
    }
  }
  .unit {
    font-size: inherit;
  }
  .format {
    input {
      font-size: inherit;
      padding: 0.9vh 0vw 0.9vh 0vw;
      width: 100%;
    }
  }
  button {
    font-size: 3vh;
  }
}

</style>
