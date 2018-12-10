<template>
  <div class="_page shopping-lists-page">
    <h1 class="title">Shopping Lists</h1>
    <button
      class="header-button"
      @click="create">
      + New List
    </button>
    <input
      class="_search"
      name="query" v-model="searchQuery" placeholder="Search">
    <base-table
      class="table"
      :data="query"
      :columns="columns"
      :filter-key="searchQuery"
      :deleteMutation="deleteMutation"
      :gqlQuery="gqlQuery">
    </base-table>
  </div>
</template>

<script>
import BaseTable from '../modules/BaseTable'
import { MY_SHOPPINGLISTS_QUERY, DELETE_SHOPPINGLIST_MUTATION } from '../../../constants/graphql'
import { apolloClient } from '../../../apollo-client'

export default {
  name: 'ShoppingListsPage',
  components: {
    BaseTable
  },
  data () {
    return {
      userId: this.$store.state.auth.userId,
      query: [],
      sortColumn: '',
      searchQuery: '',
      columns: [
        {dbField: 'name', title: 'name'},
        {dbField: 'createdAt', title: 'createdAt'}
      ],
      deleteMutation: DELETE_SHOPPINGLIST_MUTATION,
      gqlQuery: MY_SHOPPINGLISTS_QUERY
    }
  },
  apollo: {
    // allUser here pulls the data from ALL_USERS_QUERY and assigns it to the data(){} object at the top of script
    allShoppingLists: {
      query: MY_SHOPPINGLISTS_QUERY,
      variables () {
        return {
          ownedById: this.$store.state.auth.userId
        }
      },
      result ({ data }) {
        // Sets variable query to the gql data for a more modular UI template
        this.query = data.allShoppingLists
      }
    }
  },
  methods: {
    create () {
      apolloClient.writeData({ data: { showCreateShoppingListModal: true } })
    }
  }
}
</script>

<style lang="scss" scoped>
.shopping-lists-page {
  display: grid;
  grid-template-rows: 10vmin 7vmin auto;
  grid-template-areas:
    "title header-button"
    "search search"
    "table table";
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
.search{
  grid-area: search;
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
