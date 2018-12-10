<template>
  <div class="_page product-templates-page">
    <h1 class="title">Products</h1>
    <input
      class="_search"
      name="query" v-model="searchQuery" placeholder="Search">
    <div class="product-create">
      <div class="label">Add Product:</div>
      <create-product
        class="create-product"
      />
    </div>
    <product-templates-table
      class="product-templates-table"
      :data="query"
      :columns="columns"
      :filter-key="searchQuery"
      >
    </product-templates-table>
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
.product-templates-page {
  display: grid;
  grid-template-rows: 10vmin 7vmin auto auto;
  grid-template-areas:
    "title title"
    "search search"
    "product-create product-create"
    "product-templates-table product-templates-table";
  background-color: white;
  ._search {
    grid-area: search;
  }
  .product-create {
    grid-area: product-create;
    font-size: 4vmin;
    display: grid;
    grid-template-areas:
      "label create-product";
    grid-template-columns: max-content auto;
    align-items: center;
    // margin-top: 2vh;
    margin-bottom: 1vh;
    .label {
      grid-area: label;
      margin-right: 1vw;
    }
    .create-product {
      grid-area: create-product;
    }
  }
  .product-templates-table {
    grid-area: product-templates-table;
  }
}
// ================================================ Mobile Styles ========================================
@media only screen and (max-width: 875px) {
.product-templates-page {
  .product-create {
    grid-template-areas:
        "label"
        "create-product";
    grid-template-columns: 1fr;
    grid-template-rows: auto auto;
    // background-color: lightblue;
    // .label {
    //   margin-right: 5vw;
    // }
  }
}
}
</style>
