<template>
  <div class="base-list">
    <div>
      <div
        class="category-section"
        v-for='(category, index) in getCategories'
        :key='index'
      >
        <div class="category">{{category}}</div>
        <ul class="items-lists">
          <div
            v-for='(row, index) in filteredData'
            :key='index'
          >
            <div v-if="row.template.category === category">
              <input
                class="checkbox"
                v-model="row.inCart"
                @change="update(row)"
                type="checkbox">
              {{`${row.quantity}`}}
              <span v-if="row.unit !== 'each' && row.unit !== null">{{row.unit}}</span>
              {{row.template.name}}
              <span v-if="row.format !== null">{{row.format}}</span>
              <button
                @click="deleteObject(row)"
              >X</button>
            </div>
          </div>
        </ul>
      </div>
    </div>

    <!-- <ul>
      <div
        v-for='(row, index) in filteredData'
        :key='index'
      >
        <div>
          <input
            v-model="row.inCart"
            @change="update(row)"
            class="checkbox"
            type="checkbox">
          {{row.name}}
          <button
            @click="deleteObject(row)"
          >X</button>
        </div>
      </div>
    </ul> -->
  </div>
</template>

<script>
import { MY_PRODUCTS_QUERY, UPDATE_PRODUCT_MUTATION, DELETE_PRODUCT_MUTATION } from '../../../constants/graphql'
export default {
  name: 'BaseList',
  props: {
    data: Array,
    filterKey: String
  },
  data: function () {
    return {

      inCart: false
    }
  },
  filters: {
    capitalize: function (str) {
      return str.charAt(0).toUpperCase() + str.slice(1)
    }
  },
  computed: {
    getCategories: function () {
      let categories = []
      this.data.forEach(x => {
        console.log('Category', x, x.template)
        if (!categories.includes(x.template.category)) {
          categories.push(x.template.category)
        }
      })
      console.log('Categories', categories)
      return categories
    },
    filteredData: function () {
      let data = this.data
      let filterKey = this.filterKey && this.filterKey.toLowerCase()
      if (filterKey) {
        data = data.filter(function (row) {
          return String(row.template['name']).toLowerCase().indexOf(filterKey) > -1 || String(row.template['category']).toLowerCase().indexOf(filterKey) > -1
        })
      }
      console.log('Filtered Data', data)
      return data
    }
  },
  methods: {
    getName (owner) {
      return owner.firstName + ' ' + owner.lastName
    },
    isCreatedAt: function (field) {
      if (field === 'createdAt') {
        return true
      } else {
        return false
      }
    },
    isUpdatedAt: function (field) {
      if (field === 'updatedAt') {
        return true
      } else {
        return false
      }
    },
    sortBy: function (key) {
      this.sortKey = key
      this.sortOrders[key] = this.sortOrders[key] * -1
    },
    update (row) {
      console.log('In Cart', row.inCart, row.id)
      this.$apollo.mutate({
        mutation: UPDATE_PRODUCT_MUTATION,
        variables: {
          id: row.id,
          inCart: row.inCart
        },
        update: (store, { data: { updateProduct } }) => {
          // Read the data from our cache for this query.
          const data = store.readQuery({
            query: MY_PRODUCTS_QUERY,
            variables: {
              listId: this.$route.params.id
            }
          })
          // // Remove item from the list
          let index = data.allProducts.findIndex(x => x.id === row.id)
          if (index !== -1) {
            data.allProducts[index]['inCart'] = row.inCart
          }
          // Take the modified data and write it back into the store.
          store.writeQuery({
            query: MY_PRODUCTS_QUERY,
            variables: {
              listId: this.$route.params.id
            },
            data
          })
        }
      }).catch((error) => {
        console.error(error)
      })
    },
    deleteObject (obj) {
      this.$apollo.mutate({
        mutation: DELETE_PRODUCT_MUTATION,
        variables: {
          id: obj.id
        },
        update: (store, { data: { deleteProduct } }) => {
          // Read the data from our cache for this query.
          const data = store.readQuery({
            query: MY_PRODUCTS_QUERY,
            variables: {
              listId: this.$route.params.id
            }
          })
          // // Remove item from the list
          const index = data.allProducts.findIndex(x => x.id === obj.id)
          if (index !== -1) {
            data.allProducts.splice(index, 1)
          }
          // Take the modified data and write it back into the store.
          store.writeQuery({
            query: MY_PRODUCTS_QUERY,
            variables: {
              listId: this.$route.params.id
            },
            data
          })
        }
      }).catch((error) => {
        console.error(error)
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.base-list {
  .category-section {
    margin-bottom: 1vmin;
  }
  .category {
    font-weight: 900;
    margin-bottom: 0.5vmin;
  }
  .items-lists {
    margin-left: 4vmin;
  }
  input {
    width: auto;
  }
  li {
    text-decoration-style: none;
  }
}

</style>
