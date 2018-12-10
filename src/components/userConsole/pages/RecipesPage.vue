<template>
  <div class="_page recipes-page">
    <h1 class="title">Recipes</h1>
    <button
      class="header-button"
      @click="create"
    >
      New Recipe
    </button>
    <input
      class="_search"
      name="query" v-model="searchQuery" placeholder="Search">
    <div class="select-shopping-list">
      <v-select
        placeholder="Add to Shopping List"
        v-model="selected"
        label="name"
        :options="allShoppingLists">
      </v-select>
    </div>
    <recipes-table
      class="table"
      :data="query"
      :columns="columns"
      :filter-key="searchQuery"
      :shoppingList="selected"
      >
    </recipes-table>
  </div>
</template>

<script>
import RecipesTable from '../modules/RecipesTable'
import { MY_RECIPES_QUERY, MY_SHOPPINGLISTS_QUERY, CREATE_RECIPE_MUTATION } from '../../../constants/graphql'
import gql from 'graphql-tag'
import { apolloClient } from '../../../apollo-client'
// import { cache } from '../../../apollo-client'
import vSelect from 'vue-select'

export default {
  name: 'RecipesPage',
  components: {
    RecipesTable, vSelect
  },
  data () {
    return {
      query: [],
      allShoppingLists: [],
      selected: {},
      sortColumn: '',
      searchQuery: '',
      isEditMode: false,
      hello: '',
      allRecipes: [],
      columns: [
        {dbField: 'name', title: 'name'}
        // {dbField: 'category', title: 'category'},
        // {dbField: 'price', title: 'price'},
        // {dbField: 'unit', title: 'unit'},
        // {dbField: 'createdAt', title: 'createdAt'}
      ],
      userId: this.$store.state.auth.userId
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
    allShoppingLists: {
      query: MY_SHOPPINGLISTS_QUERY,
      variables () {
        return {
          ownedById: this.$store.state.auth.userId
        }
      },
      result ({ data }) {
        // Sets variable query to the gql data for a more modular UI template
        console.log('Shopping Lists', data.allShoppingLists)
      }
    },
    isEditMode: gql`
      query {
        isEditMode @client
      }
    `,
    showCreateRecipeModal: gql`
      query {
        showCreateRecipeModal @client
      }
    `
  },
  methods: {
    create () {
      this.$apollo.mutate({
        mutation: CREATE_RECIPE_MUTATION,
        variables: {
          ownedById: this.userId,
          name: 'New Recipe',
          steps: []
        },
        update: (store, { data: { createRecipe } }) => {
          let path = createRecipe.__typename.toLowerCase() + 's' + '/' + createRecipe.id
          this.$router.push({ path: path })
          // Pull data from the stored query
          console.log('Store from CreateRecipe', store)
          const data = store.readQuery({
            query: MY_RECIPES_QUERY,
            variables: {
              ownedById: this.userId
            }
          })
          // We add the new data
          data.allRecipes.push(createRecipe)
          console.log('Test', data)
          // We update the cache
          store.writeQuery({
            query: MY_RECIPES_QUERY,
            variables: {
              ownedById: this.userId
            },
            data: data })
        }
      }).catch((error) => {
        console.error(error)
      })
      apolloClient.writeData({ data: { isEditMode: true } })
    }
  }
}
</script>

<style lang="scss" scoped>
.recipes-page {
  display: grid;
  grid-template-rows: 10vh 7vh auto auto;
  grid-template-areas:
    "title header-button"
    "search search"
    "select-shopping-list select-shopping-list"
    "table table";
  background-color: white;
}
.title {
  grid-area: title;
}
.header-button {
  grid-area: header-button;
  width: 15vw;
  height: 5vh;
  justify-self: end;
}
._search{
  grid-area: search;
}
.select-shopping-list {
  grid-area: select-shopping-list;
  // margin-top: 1vh;
}
.table {
  grid-area: table;
}
</style>
