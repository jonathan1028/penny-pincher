<template>
  <div class="_page shopping-list-page">
    <h1 class="title">{{shoppingList.name}}</h1>
    <input
      class="_search"
      name="query"
      v-model="searchQueryIn"
      placeholder="Search">
    <div class="recipes-section">
      <div class="recipes-label">Recipes</div>
      <div class="recipes-add-row">
        <div class="label">Add Recipe:</div>
        <v-select
          class="recipes-selector"
          placeholder="Select Recipe"
          v-model="recipe"
          label="name"
          :options="allRecipes">
        </v-select>
        <button class="recipes-add-button _button1"
          @click="addRecipe(recipe)"
        >
          +
        </button>
      </div>
      <div class="recipes-list">
        <div
          v-for="(recipe, index) in shoppingList.recipes"
          :key="index">
          <div>
            <icon class="bullet" :icon="['far', 'lemon']"/>
            {{recipe.name}}
            <button
              @click="deleteRecipe(recipe)"
            >X</button>
          </div>
        </div>
      </div>
    </div>
    <div class="items-section">
      <div class="items-label">Items</div>
      <div class="product-add-row">
        <div class="label">Add Item:</div>
        <product-add
          class="product-add"
          :listType="'shoppingList'"
        />
      </div>
      <product-list
        class="items-list"
        :searchQuery="searchQueryOut"
      />
    </div>
  </div>
</template>

<script>
import ProductAdd from '../modules/ProductAdd'
import ProductList from '../modules/ProductList'
import CreateProduct from '../modules/CreateProduct'
import { MY_RECIPES_QUERY, CREATE_PRODUCT_MUTATION, MY_PRODUCTS_QUERY, ADD_TO_RECIPESONSHOPPINGLIST_MUTATION, MY_SHOPPINGLISTS_QUERY } from '../../../constants/graphql'
import vSelect from 'vue-select'

export default {
  name: 'ShoppingListPage',
  components: {
    ProductAdd, ProductList, CreateProduct, vSelect
  },
  data () {
    return {
      searchQueryIn: '',
      allRecipes: [],
      allProducts: [],
      recipe: {},
      recipes: [
        {
          name: 'test1'
        }
      ],
      userId: this.$store.state.auth.userId,
      shoppingList: JSON.parse(localStorage.getItem('shoppinglist'))
    }
  },
  computed: {
    searchQueryOut: function () {
      return this.searchQueryIn
    }
  },
  apollo: {
    allRecipes: {
      query: MY_RECIPES_QUERY,
      variables () {
        return {
          ownedById: this.$store.state.auth.userId
        }
      },
      result ({ data }) {
        // Sets variable query to the gql data for a more modular UI template
        this.query = data.allRecipes
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
    },
    allShoppingLists: {
      query: MY_SHOPPINGLISTS_QUERY,
      variables () {
        return {
          ownedById: this.$store.state.auth.userId
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
    addRecipe (recipe) {
      // Add recipe to local shoppingList
      this.shoppingList.recipes.push(recipe)

      // Add recipe to shoppingList in DB
      this.$apollo.mutate({
        mutation: ADD_TO_RECIPESONSHOPPINGLIST_MUTATION,
        variables: {
          shoppingListId: this.$route.params.id,
          recipeId: recipe.id
        },
        update: (store, { data: { createProduct } }) => {
          // Pull data from the stored query
          const data = store.readQuery({
            query: MY_SHOPPINGLISTS_QUERY,
            variables: {
              ownedById: this.userId
            }
          })
          // We add the new data
          const index = data.allShoppingLists.findIndex(x => x.id === this.$route.params.id)
          if (index !== -1) {
            data.allShoppingLists[index].recipes.push(recipe)
          }
          // We update the cache
          store.writeQuery({
            query: MY_SHOPPINGLISTS_QUERY,
            variables: {
              ownedById: this.userId
            },
            data: data })
        }
      }).catch((error) => {
        console.error(error)
      })

      recipe.ingredients.forEach(x => {
        console.log('Ingredient', x.template.name)
        this.$apollo.mutate({
          mutation: CREATE_PRODUCT_MUTATION,
          variables: {
            templateId: x.template.id,
            shoppingListId: this.$route.params.id,
            quantity: x.quantity,
            format: x.format,
            unit: x.unit
          },
          update: (store, { data: { createProduct } }) => {
            const data = store.readQuery({
              query: MY_PRODUCTS_QUERY,
              variables: {
                listId: this.$route.params.id
              }
            })
            // // We add the new data
            data.allProducts.push(createProduct)

            // // We update the cache
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
      })
    }
  },
  beforeCreate () {
    // this.shoppingList = JSON.parse(localStorage.getItem('shoppinglist'))
    this.id = this.$route.params.id
    console.log('User', this.user, this.id)
    // console.log('List', this.shoppingList)
  }
}
</script>

<style lang="scss" scoped>
.shopping-list-page {
  display: grid;
  grid-template-rows: 10vmin 7vmin auto auto;
  grid-template-areas:
    "title title"
    "search search"
    "recipes-section recipes-section"
    "items-section items-section";
  .recipes-section {
    grid-area: recipes-section;
    display: grid;
    grid-template-columns: max-content auto;
    grid-template-rows: auto auto auto;
    grid-template-areas:
      "recipes-label recipes-label"
      "recipes-add-row recipes-add-row"
      "recipes-list recipes-list";
    .recipes-add-row {
      grid-area: recipes-add-row;
      font-size: 3vmin;
      margin-left: 3vw;
      display: grid;
      grid-template-areas:
        "label recipes-selector recipes-add-button";
      grid-template-columns: max-content auto 9%;
      align-items: center;
      margin-bottom: 1vh;
      .label {
        grid-area: label;
        margin-right: 1vw;
        font-size: 2.5vmin;
      }
      .recipes-selector {
        grid-area: recipes-selector;
      }
    }
    .recipes-label {
      grid-area: recipes-label;
      font-size: 4vmin;
    }
    .recipes-selector {
      grid-area: recipes-selector;
      font-size: 2vmin;
    }
    .recipes-add-button {
      grid-area: recipes-add-button;
      height: 100%
    }
    .recipes-list {
      grid-area: recipes-list;
      margin-left: 5vw;
    }
  }
  .items-section {
    grid-area: items-section;
    .items-label {
      grid-area: items-label;
      font-size: 4vmin;
    }
    .product-add-row {
      grid-area: product-add-row;
      font-size: 3vmin;
      margin-left: 3vw;
      display: grid;
      grid-template-areas:
        "label product-add";
      grid-template-columns: max-content auto;
      align-items: center;
      margin-bottom: 1vh;
      .label {
        grid-area: label;
        margin-right: 1vw;
        font-size: 2.5vmin;
      }
      .product-add {
        grid-area: product-add;
      }
    }
    .items-list {
      grid-area: items-list;
      height: 50vh;
      overflow-y: auto;
      margin-left: 3vw;
    }

  }
}
.title {
  grid-area: title;
}
._search {
  grid-area: search;
}
// =================== Mobile ==================
@media only screen and (max-width: 1000px) {
.shopping-list-page {
  display: grid;
  grid-template-rows: 10vmin 7vmin auto auto;
  grid-template-areas:
    "title title"
    "search search"
    "product-add-row product-add-row"
    "label label"
    "product-list product-list"
    "table table";
  .product-add-row {
    grid-template-areas:
        "label"
        "product-add";
    grid-template-columns: 1fr;
    grid-template-rows: auto auto;
  }
}
}
</style>
