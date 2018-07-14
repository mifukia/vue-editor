<template>
  <div id="editor">
    <h1>エディター画面</h1>
    <ul>
      <li>{{user.displayName}}</li>
      <li class="myphoto"><img :src="user.photoURL" alt=""></li>
    </ul>
    <button @click="logout">
      ログアウト
    </button>
  </div>
</template>

<script>
export default {
  name: 'editor',
  props:['user'],
  data () {
    return {
      
    }
  },
  methods: {
    logout(){
      firebase.auth().signOut();
    }
  }
}
</script>

<style lang="scss">
  ul{
    li{
      &.myphoto{
        img{
          width: 100px;
        }
      }
    }
  }
</style>
