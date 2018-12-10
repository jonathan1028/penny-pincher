<template>
  <div class="_page shopping-list-page">
    <h1 class="title">{{shoppingList.name}}</h1>
    <input
      class="_search"
      name="query"
      v-model="searchQueryIn"
      placeholder="Search">
    <product-add
      class="product-add"
    />
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

export default {
  name: 'ReadShoppingListPage',
  beforeCreate () {
    this.shoppingList = JSON.parse(localStorage.getItem('shoppinglist'))
    this.id = this.$route.params.id
    console.log('User', this.user, this.id)
    console.log('List', this.shoppingList)
  },
  components: {
    ProductAdd, ProductList
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
  grid-template-rows: 10vh 7vh auto auto;
  grid-template-areas:
    "title title"
    "search search"
    "product-add product-add"
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
.product-add {
  grid-area: product-add;
}
.product-list {
  grid-area: product-list;
}
.label {
  grid-area: label;
  font-size: 4vmin;
}
</style>
