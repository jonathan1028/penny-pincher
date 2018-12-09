<template>
  <div class="_page shopping-lists-page">
    <h1 class="title">Shopping Lists</h1>
      <button
        class="create-button"
        @click="create">
        + New Shopping List
      </button>
      <input
        class="search"
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
.page {
  display: grid;
  grid-template-areas:
    "title create-button"
    "search search"
    "select-shopping-list select-shopping-list"
    "table table";
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
