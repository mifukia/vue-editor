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
    <div class="editorWrapper">
      <textarea class="markdown" v-model="markdown"></textarea>
      <div class="preview" v-html="preview()"></div>
    </div>
  </div>
</template>

<script>
import marked from "marked";
export default {
  name: 'editor',
  props:['user'],
  data () {
    return {
      markdown:""
    }
  },
  methods: {
    logout(){
      firebase.auth().signOut();
    },
    preview(){
      return marked(this.markdown);
    }
  }
}
</script>

<style lang="scss">
  ul{
    li{
      list-style: none;
      &.myphoto{
        img{
          width: 100px;
        }
      }
    }
  }
  .editorWrapper{
    display:flex;
  }
  .markdown{
    width:50%;
    height:500px;
  }
  .preview{
    width: 50%;
    text-align: left;
  }
</style>
