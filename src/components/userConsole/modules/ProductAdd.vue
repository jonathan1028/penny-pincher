<template>
  <div class="product-add">
    <div class="errors">
      <div
        class="error-item"
        v-for="(error, index) in errors"
        :key="index">
        {{error}}
      </div>
    </div>
    <div class="product-input">
      <input
        class="qty"
        v-model="quantity"
        type="text"
        placeholder="Qty">
      <v-select
        class="unit"
        placeholder="Unit (Optional)"
        v-model="selectedUnit"
        label="name"
        :options="units">
      </v-select>
      <v-select
        class="product-name"
        placeholder="Add Product"
        v-model="selected"
        label="name"
        :options="query">
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

  </div>
</template>

<script>
import { CREATE_PRODUCT_MUTATION, ALL_PRODUCTTEMPLATES_QUERY, MY_PRODUCTS_QUERY, MY_RECIPES_QUERY } from '../../../constants/graphql'
import vSelect from 'vue-select'

export default {
  name: 'SelectProduct',
  components: {
    vSelect
  },
  props: {
    listType: String
  },
  data () {
    return {
      query: [],
      units: ['each', 'pinch', 'tsp', 'tbsp', 'fl oz', 'cup', 'pt', 'qt', 'gal', 'oz', 'lb'],
      selectedUnit: null,
      format: null,
      price: null,
      selected: null,
      quantity: null,
      userId: this.$store.state.auth.userId,
      errors: []
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
      let quantity = parseFloat(this.quantity)
      let shoppingListId = null
      let recipeId = null
      if (this.listType === 'shoppingList') {
        shoppingListId = this.$route.params.id
      }
      if (this.listType === 'recipe') {
        recipeId = this.$route.params.id
      }
      if (!isNaN(quantity) && this.selected !== null) {
        this.$apollo.mutate({
          mutation: CREATE_PRODUCT_MUTATION,
          variables: {
            templateId: this.selected.id,
            shoppingListId: shoppingListId,
            recipeId: recipeId,
            quantity: quantity,
            format: this.format,
            unit: this.selectedUnit
          },
          update: (store, { data: { createProduct } }) => {
            if (this.listType === 'shoppingList') {
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
            if (this.listType === 'recipe') {
              const data = store.readQuery({
                query: MY_RECIPES_QUERY,
                variables: {
                  ownedById: this.userId
                }
              })
              console.log('Recipes Query', data)
              // We add the new data
              const index = data.allRecipes.findIndex(x => x.id === this.$route.params.id)
              if (index !== -1) {
                data.allRecipes[index].ingredients.push(createProduct)
              }
              console.log('Test', data)
              // We update the cache
              store.writeQuery({
                query: MY_RECIPES_QUERY,
                data: data
              })
            }
            this.errors = []
            this.selected = null
            this.quantity = null
            this.selectedUnit = null
            this.format = null
          }
        }).catch((error) => {
          console.error(error)
        })
      }
      // Add errors
      console.log('Type of', isNaN(quantity))
      if (isNaN(quantity)) {
        this.errors.push('Quantity must be a number.')
      }
      if (this.selected === null) {
        this.errors.push('A product must be selected to add ingredient.')
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.product-add {
  .product-input {
    width: 100%;
    display: grid;
    grid-template-columns: 10% 20% 30% 30% 10%;
    grid-template-areas:
      "qty unit product-name format";
    font-size: 2vmin;
    border: 1px solid lightgray;
    align-items: center;
    input {
      font-size: inherit;
      padding: 0.8vh 0vw 0.8vh 0.5vw;
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
    button {
      height: 100%;
    }
  }
  .errors {
    .error-item {
      color: red;
      font-size: 2vmin;
    }
  }
}

// ================================================ Mobile Styles ========================================
@media only screen and (max-width: 767px) {
  .product-add {
    .product-input {
      grid-template-columns: 10% 30% 40% 20%;
      grid-template-areas:
        "qty unit product-name product-name"
        "format format format .";
      .qty {
        border-bottom: 0.15vh solid lightgray;
        border-top: none;
        border-left: none;
        border-right: none;
        height: 2vh;
      }
    }
  }
}
</style>
