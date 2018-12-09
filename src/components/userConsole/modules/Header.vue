<template>
  <div class="header _card">
    <!-- Logo -->
    <div class="logo">Penny Pincher</div>
    <div class="links">
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
    </div>
    <div class="logout">
      <div class="username">
        {{user.email}}
      </div>
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
// ================= Web Styles ===========
.header {
    padding: 1vh 2vw;
    display: grid;
    grid-template-areas:
      "logo links logout";
    grid-template-rows: auto;
    grid-template-columns: auto auto auto;
    .logo {
      grid-area: logo;
      // border: 1px solid black;
    }
    .links {
      grid-area: links;
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-content: center;
      // border: 1px solid black;
      justify-self: center;
    }
    .logout {
      display: flex;
      grid-area: logout;
      align-items: center;
      // border: 1px solid black;
      justify-self: end;
      button {
        font-size: 2vh;
        margin-left: 1vw;
      }
    }
    .username {
      // display: none;
      // border: 1px solid black;
    }
  }
// ================= Mobile Styles ===========
@media only screen and (max-width: 767px) {
  .header {
    padding: 1vh 3vw;
    display: grid;
    grid-template-areas:
      "logo  logout"
      "links links";
    grid-template-rows: auto auto;
    grid-template-columns: auto auto;
    .logo {
      grid-area: logo;
      // border: 1px solid black;
    }
    .links {
      grid-area: links;
      // border: 1px solid black;
    }
    .logout {
      grid-area: logout;
      .username {
        display: none;
      }
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
</style>
