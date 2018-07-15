<template>
  <div id="editor">
    <div class="userArea">
      <ul>
        <li class="myphoto"><img :src="user.photoURL" alt=""></li>
        <li>{{user.displayName}}</li>
      </ul>
      <button @click="logout" class="logout">
        ログアウト
      </button>
    </div>
    <main class="main">
      <div class="memoListWrapper">
        <div class ="memoList" v-for ="(memo, index) in memos" @click ="selectMemo( index)" :data-selected ="index == selectedIndex">
          <p class="memoTitle">
            {{displayTitle(memo.markdown)}}
          </p>
        </div>
        <button class="addMemoBtn" @click="addMemo">
          メモの追加
        </button>
      </div>
      <textarea class="markdown" v-model="memos[selectedIndex].markdown"></textarea>
      <div class="preview" v-html="preview()"></div>
    </main>
  </div>
</template>

<script>
import marked from "marked";
export default {
  name: 'editor',
  props:['user'],
  data () {
    return {
      memos:[
        {
          markdown:""
        }
      ],
      selectedIndex:0
    }
  },
  methods: {
    logout(){
      firebase.auth().signOut();
    },
    addMemo(){
      this.memos.push({
        markdown:"無題のメモ"
      })
      console.log(this.memos)
    },
    selectMemo(index){
      this.selectedIndex = index;
    },
    preview(){
      return marked(this.memos[this.selectedIndex].markdown);
    },
    displayTitle(text){
      return text.split(/\n/)[0];
    }
  }
}
</script>

<style lang="scss">
  #editor{
    max-width: 1200px;
    margin: 0 auto;
  }
  .userArea{
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .logout{
    height: 3em;
  }
  ul{
    display: flex;
    align-items: center;
    padding: 0;
    margin: 0;
    li{
      list-style: none;
      &.myphoto{
        margin-right: 1em;
        img{
          width: 50px;
        }
      }
    }
  }
  .main{
    display: flex;
    justify-content: space-between;
  }
  .memoListWrapper{
    width: 19%;
    border-top: 1px solid #000;
  }
  .memoList{
    padding: 10px;
    box-sizing: border-box;
    text-align: left;
    border-bottom: 1px solid #000;
    &:nth-child(even){
      background: #ccc;
    }
    &[data-selected="true"]{
      background: #ccf;
    }
  }
  .memoTitle{
    height: 1.5em;
    margin: 0;
  }
  .addMemoBtn{
    margin-top: 20px;
  }
  .markdown{
    width: 40%;
    height: 500px;
  }
  .preview{
    width: 40%;
    text-align: left;
    border: 1px solid #ccf;
  }
</style>
