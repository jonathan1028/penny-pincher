<template>
  <div class="_page shopping-list-page">
    <h1 class="title">{{shoppingList.name}}</h1>
    <input
      class="_search"
      name="query"
      v-model="searchQueryIn"
      placeholder="Search">
    <!-- <product-add
      class="product-add"
    /> -->
    <div class="product-add-row">
      <div class="label">Add Item:</div>
      <product-add
        class="product-add"
        :listType="'shoppingList'"
      />
    </div>
    <div class="label">Items</div>
    <product-list
      class="product-list"
      :searchQuery="searchQueryOut"
    />
  </div>
</template>

<script>
import ProductAdd from '../modules/ProductAdd'
import ProductList from '../modules/ProductList'
import CreateProduct from '../modules/CreateProduct'

export default {
  name: 'ReadShoppingListPage',
  beforeCreate () {
    this.shoppingList = JSON.parse(localStorage.getItem('shoppinglist'))
    this.id = this.$route.params.id
    console.log('User', this.user, this.id)
    console.log('List', this.shoppingList)
  },
  components: {
    ProductAdd, ProductList, CreateProduct
  },
  data () {
    return {
      searchQueryIn: ''
    }
  },
  computed: {
    searchQueryOut: function () {
      return this.searchQueryIn
    }
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
    "product-add-row product-add-row"
    "label label"
    "product-list product-list"
    "table table";
}
.title {
  grid-area: title;
}
._search {
  grid-area: search;
}
.product-add-row {
    grid-area: product-add-row;
    font-size: 4vmin;
    display: grid;
    grid-template-areas:
      "label product-add";
    grid-template-columns: max-content auto;
    align-items: center;
    margin-bottom: 1vh;
    .label {
      grid-area: label;
      margin-right: 1vw;
    }
    .product-add {
      grid-area: product-add;
    }
  }
.product-list {
  grid-area: product-list;
}
.label {
  grid-area: label;
  font-size: 4vh;
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
