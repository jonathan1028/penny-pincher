<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style lang="scss">
* {
  margin: 0px;
  padding: 0px;
}
html {
  background-color: whitesmoke;
  font-family: 'Open Sans', sans-serif;
  max-width: 100vw;
  margin: 0px;
  padding: 0px;
}

/* ------------------------- Application Variables ------------------------- */
:root {
  --page-height: 100vh;
  --page-width: 100vw;
  --page-padding-width: 4vw;

  --font-color1: #47535e;
  --font-color3: #f7ca44;
  --font-color4: lightgray;

  --background-color1: #fc0;
  --background-color2: white;
  --background-color3: #555;
  --background-color4: #e7e9ea;
}

._page {
  padding: 3vh var(--page-padding-width);
  background-color: white;
  width: 70vw;
  min-height: 86vh;
  margin: 1vh auto;
  h1 {
    font-size: 6vmin;
    margin-bottom: 1vh;
  }
}

._card {
  background-color: white;
  padding: 1%;
  border: 1px solid #BFBFBF;
  background-color: white;
  box-shadow: 3px 3px 3px 3px #aaaaaa;
  -webkit-box-shadow: 0 1px 2px #777;
  -moz-box-shadow: 0 2px 1px #777;
  box-shadow: 0 2px 1px #777;

}
._search {
  font-size: 2.5vmin;
  border-bottom: 0.15vh solid lightgray;
  width: 20vw;
  margin-bottom: 2vmin;
}
form{
  padding: 0px;
  margin: 0px;
}

form > button{
  margin-top: 40px;
}

label {
  // width: 30%;
  // display: block;
  // text-align: right;
  // padding-right: 5%;
  // display: flex;
  // align-items: center;
  // justify-content: flex-end;
}

.field {
  // width: 50%;
  // margin-bottom: 1%;
  // display: flex;
  // align-items: center;
}

input{
  // width: 70%;
  // border: .5px solid lightgray;
  // padding: 1%;
}

input:hover{
  // border: .5px solid gray;

}

button{
  cursor: pointer;
  margin-bottom: 1%;
  -webkit-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  -moz-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
}

button:hover{
  background-color: rgb(250, 250, 250);
  -webkit-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  -moz-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
  box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
}
._box-shadow1 {
  -webkit-box-shadow: 0 .25vh 1vh #777;
  -moz-box-shadow: 0 .25vh 1vh #777;
  box-shadow: 0 .25vh 1vh #777;
}
._box-shadow2 {
  -webkit-box-shadow: 0 .25vh 1vh #777;
  -moz-box-shadow: 0 .25vh 1vh #777;
  box-shadow: 0 .25vh 1vh #777;
}
// ================================================ Mobile Styles ========================================
@media only screen and (max-width: 767px) {
// width: 93%;
// height: 100vh;
._page {
  width: calc(100vw - 2 * var(--page-padding-width));
  // height: 90vh;
  margin-top: 0vh;
}
}
</style>
