<template>
  <div class="header">
    <!-- Logo -->
    <div class="logo">Penny Pincher</div>
    <router-link
      class="link1"
      to="/products">Products</router-link>
    <!-- <div>|</div> -->
    <router-link
      class="link2"
      to="/shoppinglists">Shopping</router-link>
    <!-- <div>|</div> -->
    <router-link
      class="link3"
      to="/recipes">Recipes</router-link>
    <div class="username">
      {{user.email}}
    </div>
    <div class="logout">
      <button
        v-if="!authenticated"
        @click="login()">
          Log In
      </button>
      <button
        v-if="authenticated"
        @click="logout()">
          Log Out
      </button>
    </div>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
import gql from 'graphql-tag'

export default {
  name: 'Header',
  data () {
    console.log('user', this.$store.state.auth.user)
    return {
      toggleCreateRecipeModal: false,
      toggleDeleteRecipeModal: false
    }
  },
  apollo: {
    showCreateRecipeModal: gql`
      query {
        showCreateRecipeModal @client
      }
    `,
    showCreateShoppingListModal: gql`
      query {
        showCreateShoppingListModal @client
      }
    `,
    showDeleteRecipeModal: gql`
      query {
        showDeleteRecipeModal @client
      }
    `
  },
  computed: mapGetters(['authenticated', 'user', 'userId']),
  methods: mapActions(['login', 'logout'])
}
</script>

<style lang="scss" scoped>
/* phones */
@media only screen and (max-width: 767px) {
  .header {
    display: grid;
    grid-template-areas:
      "logo link1 link2 link3 logout";
    grid-template-rows: auto;
    grid-template-columns: 20vw 20vw 20vw 20vw 20vw;
    .logo {
      grid-area: logo;
      // border: 1px solid black;
    }
    .link1 {
      grid-area: link1;
      // border: 1px solid black;
    }
    .link2 {
      grid-area: link2;
      // border: 1px solid black;
    }
    .link3 {
      grid-area: link3;
      // border: 1px solid black;
    }
    .logout {
      grid-area: logout;
      // border: 1px solid black;
      justify-self: end;
      button {
        font-size: 2vh;
      }
    }
    .username {
      display: none;
      border: 1px solid black;
    }
  }
}
.header {
  padding: 1vh 2vw;
    display: grid;
    grid-template-areas:
      "logo link1 link2 link3 logout";
    grid-template-rows: auto;
    grid-template-columns: 18vw 20vw 20vw 20vw 18vw;
    .logo {
      grid-area: logo;
      // border: 1px solid black;
    }
    .link1 {
      grid-area: link1;
      // border: 1px solid black;
    }
    .link2 {
      grid-area: link2;
      // border: 1px solid black;
    }
    .link3 {
      grid-area: link3;
      // border: 1px solid black;
    }
    .logout {
      grid-area: logout;
      // border: 1px solid black;
      justify-self: end;
      button {
        font-size: 2vh;
      }
    }
    .username {
      // display: none;
      // border: 1px solid black;
    }
  }

</style>
