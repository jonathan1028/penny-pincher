<template>
  <div class="_page">
    <h1>Products</h1>
    <div>
      <input 
        class="_search"
        name="query" v-model="searchQuery" placeholder="Search">
      <div class="row">
        <div class="label">Add Product:</div>
        <create-product></create-product>
      </div>
      <product-templates-table
        :data="query"
        :columns="columns"
        :filter-key="searchQuery"
        >
      </product-templates-table>
    </div>
  </div>
</template>

<script>
import ProductTemplatesTable from '../modules/ProductTemplatesTable'
import CreateProduct from '../modules/CreateProduct'
import { ALL_PRODUCTTEMPLATES_QUERY } from '../../../constants/graphql'
export default {
  name: 'ProductTemplatesPage',
  components: {
    ProductTemplatesTable, CreateProduct
  },
  data () {
    return {
      query: [],
      sortColumn: '',
      searchQuery: '',
      columns: [
        {dbField: 'name', title: 'name'},
        {dbField: 'category', title: 'category'},
        {dbField: 'price', title: 'price'},
        {dbField: 'pricingUnit', title: 'Pricing Unit'},
        {dbField: 'createdAt', title: 'createdAt'}
      ]
    }
  },
  apollo: {
    // allUser here pulls the data from ALL_USERS_QUERY and assigns it to the data(){} object at the top of script
    allProductTemplates: {
      query: ALL_PRODUCTTEMPLATES_QUERY,
      variables () {
        return {
          shoppingListId: this.$route.params.id
        }
      },
      result ({ data }) {
        // Sets variable query to the gql data for a more modular UI template
        this.query = data.allProductTemplates
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.row {
  font-size: 4vmin;
  display: grid;
  grid-template-columns: max-content auto;
  align-items: center;
  margin-top: 2vh;
  .label {
    margin-right: 1vw;
  }
}
</style>
