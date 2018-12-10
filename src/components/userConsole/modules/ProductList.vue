<template>
  <div class="page">
    <base-list
      :data="query"
      :filter-key="searchQuery"
      >
    </base-list>
  </div>
</template>

<script>
import BaseList from '../modules/BaseList'
import { MY_PRODUCTS_QUERY } from '../../../constants/graphql'
export default {
  name: 'ProductList',
  components: {
    BaseList
  },
  props: {
    searchQuery: String
  },
  data () {
    return {
      query: []
      // searchQuery: 'eggs'
    }
  },
  apollo: {
    // allUser here pulls the data from ALL_USERS_QUERY and assigns it to the data(){} object at the top of script
    allProducts: {
      query: MY_PRODUCTS_QUERY,
      variables () {
        return {
          listId: this.$route.params.id || null
        }
      },
      result ({ data }) {
        // Creates clone of data because Apollo data is read only
        this.query = JSON.parse(JSON.stringify(data.allProducts))
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.page {
  height: 60vh;
  overflow-y: auto;
  padding: 0vh 2vw;
  background-color: white;
}
</style>
