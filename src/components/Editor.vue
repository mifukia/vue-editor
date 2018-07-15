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
        <div class ="memoList" v-for ="(memo, index) in memos" @click ="selectMemo(index)" :data-selected ="index == selectedIndex">
          <p class="memoTitle">
            {{displayTitle(memo.markdown)}}
          </p>
        </div>
        <button class="addMemoBtn" @click="addMemo">
          メモの追加
        </button>
        <button class="deleteMemoBtn" v-if="memos.length>1" @click="deleteMemo">
          選択中のメモの削除
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
          markdown:"無題のメモ"
        }
      ],
      selectedIndex:0,
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
    deleteMemo(){
      /*
      メモを削除
      http://js.studio-kingdom.com/javascript/array/splice

      ※nowDeleteにspliceの返り値で、削除された配列が含まれる配列を格納しとく。
      後で使うかもしれないから
      */
      const nowDelete = this.memos.splice(this.selectedIndex,1);
      if(this.selectedIndex > 0){
        this.selectedIndex --;
      }
      console.log(nowDelete)
    },
    selectMemo(index){
      //複数のメモから一つを選択する
      this.selectedIndex = index;
    },
    preview(){
      //マークダウンに変換
      return marked(this.memos[this.selectedIndex].markdown);
    },
    displayTitle(text){
      /*改行コードを区切り文字として配列にして、一行目のテキストだけを取り出す
      https://www.sejuku.net/blog/27672
      */
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
    white-space: nowrap;
    overflow: hidden;
    cursor: pointer;
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
