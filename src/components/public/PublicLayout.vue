<template>
  <div class="layout">
    <div class="header">
        <div class="main-nav">
          <!-- Logo -->
          <div class="logo">Penny Pincher</div>
          <!-- Main navigation -->
          <div class="nav-buttons">
            <!-- <router-link to="/admin">About</router-link>
            <div>|</div>
            <router-link to="/newusers">Contact</router-link> -->
          </div>
        </div>
        <!-- Login buttons   -->
        <router-link to="/login">Login</router-link>
        <!-- <button
          v-if="!authenticated"
          @click="login()">
            Log In
        </button> -->

        <!-- <button
          v-if="authenticated"
          @click="logout()">
            Log Out
        </button> -->
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'PublicHeader',
  data () {
    return {
    }
  }
}
</script>

<style lang="scss" scoped>
.layout{
  display: grid;
  grid-template-rows: 5vh auto;
  width: 100vw;
}
.header{
  width: 96vw;
  background-color: white;
  // top: 0;
  -webkit-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  -moz-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  margin: 0 auto;
  padding: 2vw;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.logo{
  margin-right: 1em;
}
.main-nav{
  display: flex;
  justify-content: flex-start;
  align-items: center;
}
.nav-buttons{
  display: flex;
  justify-content:space-between;
  align-items: center;
  color: #3273dc;
  cursor: pointer;
  text-decoration: none;
}
.nav-buttons a{
  color: #3273dc;
  text-decoration: none;
}
.nav-buttons a:visited{
  color: #3273dc;
  text-decoration: none;
}
.nav-buttons a:hover{
  color: gray;
  text-decoration: none;
}
.authenticated-nav{
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.login a{
   color: #3273dc;
   text-decoration: none;
}
.login a:visited{
  color: #3273dc;
  text-decoration: none;
}
.login a:hover{
  color: gray;
}
</style>
