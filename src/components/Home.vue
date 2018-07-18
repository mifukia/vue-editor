<template>
  <div id="home">
    <div class="titleWrap">
      <h1>{{msg}}</h1>
      <button @click="googleLogin">
          Google アカウントでログイン
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'home',
  data () {
    return {
      msg: 'Welcome to MyMarkdown'
    }
  },
  methods:{
    googleLogin(){
      firebase
        .auth()
        .signInWithRedirect(new firebase.auth.GoogleAuthProvider())
    }
  }
}
</script>

<style lang="scss">
  #home{
    background: url(../assets/bg.png) no-repeat center top;
    background-size: cover;
    height: 100vh;
    position: relative;
  }
  .titleWrap{
    position: absolute;
    bottom:10%;
    right:10%;
    h1{
      color: #fff;
      font-size: 34px;
      font-weight: bold;
    }
  }
</style>
