<template>
  <div class="_page recipes-page">
      <h1 class="title">Recipes</h1>
      <button
        class="create-button"
        @click="create"
      >
        New Recipe
      </button>
      <input
        class="search"
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
import { MY_RECIPES_QUERY, MY_SHOPPINGLISTS_QUERY } from '../../../constants/graphql'
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
      ]
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
      apolloClient.writeData({ data: { showCreateRecipeModal: true } })
    }
  }
}
</script>

<style lang="scss" scoped>
.recipes-page {
  display: grid;
  grid-template-areas:
    "title create-button"
    "search search"
    "select-shopping-list select-shopping-list"
    "table table";
  background-color: white;
}
.title {
  grid-area: title;
}
.create-button {
  grid-area: create-button;
  width: 15vw;
  justify-self: end;
}
.search{
  grid-area: search;
  width: 38%;
  font-size: 3vh;
  border: 0.15vh solid lightgray;
}
.select-shopping-list {
  grid-area: select-shopping-list;
  margin-top: 1vh;
}
.table {
  grid-area: table;
}
.adminPage {
  margin: 0vh 5vw;
}
</style>
