<template>
  <div class="_page recipe-page">
    <div class="page-header">
      <div class="page-title">
        <span v-if="!isEditMode">{{Recipe.name}}</span>
        <span v-if="isEditMode">
          <input
            v-model="Recipe.name"
            type="text"
            placeholder="Recipe Name"
            @change="update(Recipe)"
            >
        </span>
      </div>
      <div class="toggle-button">
        <button
          @click="toggleEditMode"
        >
          <span v-if="isEditMode">View</span>
          <span v-if="!isEditMode">Edit</span>
        </button>
      </div>
    </div>
    <div class="rating section-title">
      <div>Rating:
        <span v-if="!isEditMode">{{Recipe.rating}}</span>
        <span v-if="isEditMode">
          <input
            v-model="Recipe.rating"
            type="number"
            placeholder=""
            @change="update(Recipe)"
            >
        </span>
      </div>
    </div>
      <input v-on:change="file" type="file" id="file" name="file" >
      <button
        @click='upload()'
      >Submit</button>
    <div class="recipe-photo section">
        <img class="recipe-photo" src="../../../assets/logo.png"/>
      </div>
    <div class="ingredients section">
      <div class="ingredients-title section-title">Ingredients:</div>
      <product-add
        class="add-ingredient"
        v-if="isEditMode"
        :listType="'recipe'"
      />
      <div
        class="ingredients-list"
        v-for='(row, index) in Recipe.ingredients'
        :key='index'
      >
        <li>
          <icon class="bullet" :icon="['far', 'lemon']"/>
          <div>
            {{`${row.quantity} ${row.unit}`}} {{row.template.name}} {{row.format}}
            <button
              v-if="isEditMode"
              @click="deleteIngredient(row)"
            >X</button>
          </div>
        </li>
      </div>
    </div>
    <div class="steps section">
      <div class="section-title">Steps:</div>
      <div v-if="isEditMode">
        <input
          v-model="newStep"
          type="text"
          placeholder="">
        <button
          @click="add()"
          >
          + Add
        </button>
      </div>
      <div
        class="steps-list"
        v-for='(row, index) in Recipe.steps'
        :key='index'
      >
        <li>
          <!-- <icon class="bullet" icon="utensils"/> -->
          <div class="bullet">{{index + 1}}.</div>
          <div>
            {{row}}
            <button
              v-if="isEditMode"
              @click="deleteStep(row)"
            >X</button>
          </div>
        </li>
      </div>
    </div>
    <div class="notes section">
      <div class="section-title">Notes:</div>
      <div v-if="isEditMode">
        <textarea
          v-model="Recipe.notes"
          rows="10"
          columns="40"
          placeholder=""
          @change="update(Recipe)"
          >
        </textarea>
      </div>
      <div
        class="notes-list"
        v-if="!isEditMode">
        {{Recipe.notes}}
      </div>
    </div>
    <button
      v-if="isEditMode"
      @click="save"
      >Save
    </button>

  </div>
</template>

<script>
import ProductAdd from '../modules/ProductAdd'
import ProductList from '../modules/ProductList'
import { GET_RECIPE_QUERY, ALL_PRODUCTTEMPLATES_QUERY, MY_RECIPES_QUERY, UPDATE_RECIPE_MUTATION, DELETE_PRODUCT_MUTATION } from '../../../constants/graphql'
import gql from 'graphql-tag'
import { apolloClient } from '../../../apollo-client'
import vSelect from 'vue-select'

export default {
  name: 'RecipePage',
  components: {
    vSelect, ProductAdd, ProductList
  },
  // beforeCreate () {
  //   // this.recipe = JSON.parse(localStorage.getItem('recipe'))
  //   this.id = this.$route.params.id
  //   console.log('User', this.user, this.id)
  //   console.log('Recipe', this.recipe)
  // },
  data () {
    return {
      // name: this.recipe.name,
      // ingredients: this.recipe.ingredients,
      query: [],
      // steps: this.recipe.steps || [],
      newStep: '',
      isEditMode: false,
      selected: '',
      ingredient: {
        quantity: 0,
        unit: '',
        format: ''
      },
      Recipe: {
      },
      userId: this.$store.state.auth.userId,
      unitOptions: ['pinch', 'tsp', 'tbsp', 'fl oz', 'cup', 'pt', 'qt', 'gal', 'oz', 'lb'],
      file: {},
      errors: []
    }
  },
  apollo: {
    // allUser here pulls the data from ALL_USERS_QUERY and assigns it to the data(){} object at the top of script
    allProductTemplates: {
      query: ALL_PRODUCTTEMPLATES_QUERY,
      result ({ data }) {
        // Sets variable query to the gql data for a more modular UI template
        this.query = JSON.parse(JSON.stringify(data.allProductTemplates))
        // this.isEditMode = data.isEditMode
      }
    },
    allRecipes: {
      query: MY_RECIPES_QUERY,
      variables () {
        return {
          ownedById: this.$store.state.auth.userId
        }
      }
    },
    Recipe: {
      query: GET_RECIPE_QUERY,
      variables () {
        return {
          id: this.$route.params.id || ''
        }
      },
      result ({ data }) {
        console.log('Recipes data', data)
        // Sets variable query to the gql data for a more modular UI template
        this.Recipe = JSON.parse(JSON.stringify(data.Recipe))
        // this.isEditMode = data.isEditMode
      }
    },
    isEditMode: gql`
      query {
        isEditMode @client
      }
    `
  },
  methods: {
    upload () {
      console.log('Upload entered')
      let data = new FormData()
      data.append('data', this.file)
      console.log('File', this.file)
    },
    toggleEditMode () {
      apolloClient.writeData({ data: { isEditMode: !this.isEditMode } })
    },
    add () {
      this.Recipe.steps.push(this.newStep)
      this.newStep = ''
      this.update()
    },
    update () {
      let rating = parseFloat(this.Recipe.rating)
      let recipe = {
        id: this.$route.params.id,
        name: this.Recipe.name,
        steps: this.Recipe.steps,
        notes: this.Recipe.notes,
        rating: rating,
        ingredients: this.Recipe.ingredients

      }
      console.log('Updated Recipe', recipe)
      this.$apollo.mutate({
        mutation: UPDATE_RECIPE_MUTATION,
        variables: {
          id: recipe.id,
          name: recipe.name,
          steps: recipe.steps,
          notes: recipe.notes,
          rating: rating
        }
      }).catch((error) => {
        console.error(error)
      })
    },
    save () {
      apolloClient.writeData({ data: { isEditMode: false } })
      this.$router.push({path: '/recipes'})
    },
    deleteIngredient (row) {
      console.log('Ingredient to delete', row)
      let index = this.Recipe.ingredients.indexOf(row)
      this.Recipe.ingredients.splice(index, 1)
      this.$apollo.mutate({
        mutation: DELETE_PRODUCT_MUTATION,
        variables: {
          id: row.id
        }
      }).catch((error) => {
        console.error(error)
      })
      console.log('Updated ingredients', index, this.Recipe.ingredients)
    },
    deleteStep (row) {
      let index = this.Recipe.steps.indexOf(row)
      this.Recipe.steps.splice(index, 1)
      this.update()
      console.log('Updated steps', index, this.Recipe.steps)
    }
  }
}
</script>

<style lang="scss" scoped>
.section {
  margin: 2vh 0vw;
  .section-title {
    font-weight: 900;
  }
}
.recipe-page {
  display: grid;
  grid-template-columns: 50% 50%;
  grid-template-areas:
    "page-header page-header"
    "rating      rating"
    "recipe-photo recipe-photo"
    "ingredients ingredients"
    "steps steps"
    "notes notes";
  .page-header {
    grid-area: page-header;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .page-title {
      font-size: 8vmin;
      input {
        font-size: 8vmin;
      }
    }
    button {
      font-size: 4vmin;
      padding: 1vh 1vw;
    }
  }
  .rating {
    grid-area: rating;
  }
  .recipe-photo {
    grid-area: recipe-photo;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    // border: 1px solid pink;
  }
  .ingredients {
    grid-area: ingredients;
    display: grid;
    // border: 1px solid red;
    grid-template-columns: 1fr 1fr;
    grid-template-areas:
      "ingredients-title ingredients-title"
      "add-ingredient add-ingredient"
      "ingredients-list ingredients-list";
    .ingredients-title {
      grid-area: ingredients-title;
    }
    .add-ingredient {
      grid-area: add-ingredient;
      // background-color: lightgreen;
      // width: 100%;
    }
    .ingredients-list {
      // grid-area: ingredients-list;
      margin-left: 1.5vw;
      li {
        display: flex;
        align-items: center;
        .bullet {
          margin-right: 1vw;
          height: 3vmin;
        }
      }
    }
  }
  .steps {
    grid-area: steps;
    .steps-list {
      margin-left: 1.5vw;
      li {
        margin-bottom: 1.5vh;
        display: flex;
        align-items: flex-start;
        .bullet {
          margin-right: 1vw;
          // margin-top: 1vh;
          font-size: 3vmin;
        }
      }
    }
    input {
       margin-left: 1.5vw;
    }
  }
  .notes {
    grid-area: notes;
    .notes-list {
      margin-left: 1.5vw;
    }
    textarea {
      margin-left: 1.5vw;
    }
  }
  .section-title {
    font-size: 3vh;
    font-weight: 900;
  }
  input, textarea {
    font-size: 2vh;
  }

}
// =================== Mobile ==================
@media only screen and (max-width: 767px) {
.recipe-page {
  display: grid;
  // width: 94vw;
  grid-template-areas:
    "page-header page-header"
    "rating      rating"
    "recipe-photo recipe-photo"
    "ingredients ingredients"
    "steps steps"
    "notes notes";
  .ingredients {
    grid-template-columns: 1fr;
    grid-template-areas:
      "ingredients-title"
      "add-ingredient"
      "ingredients-list";
  }
}
}
</style>
