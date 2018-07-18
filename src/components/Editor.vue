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
        <draggable :list="memos">
          <div class ="memoList" v-for ="(memo, index) in memos" @click ="selectMemo(index)" :data-selected ="index == selectedIndex">
            <p class="memoTitle">
              {{displayTitle(memo.markdown)}}
            </p>
        </div>
        </draggable>
        
        <button class="addMemoBtn" @click="addMemo">
          メモの追加
        </button>
        <button class="deleteMemoBtn" v-if="memos.length>1" @click="deleteMemo">
          選択中のメモの削除
        </button>
        <button class="saveMemosBtn" @click="saveMemos">
          メモの保存
        </button>
      </div>
      <textarea class="markdown" v-model="memos[selectedIndex].markdown"></textarea>
      <div class="preview markdown-body" v-html="preview()"></div>
    </main>
  </div>
</template>

<script>
import draggable from "vuedraggable"
import marked from "marked";
export default {
  name: 'editor',
  props:['user'],
  components:{
    draggable,
  },
  data () {
    return {
      memos:[
        {
          markdown:""
        }
      ],
      selectedIndex:0,
    }
  },
  created(){
    /*
    firebaseのRealtimeDBから.once('value')で一回だけの読み込みを行う
    Promise形式でデータの読み込みが終わり次第、then内に記載の関数が結果とともに読み込まれる
    */
    firebase
      .database()
      .ref('memos/'+this.user.uid)
      .once('value')
      .then(result => {
        if(result.val()){//初めて利用するユーザーの場合結果はnullなので。
          this.memos = result.val();
        }
      })
  },
  mounted:function(){//コンポーネントの描画が完了したタイミング
    // ctrl + s でセーブ処理
    document.onkeydown = e => {
        if (e.ctrlKey ){
          if (e.key == "s"){
              this.saveMemos();
              return false;
          }
        }
    }  
  },
  beforeDestroy(){//コンポーネントが破棄されたタイミング
    //キーダウンの設定を消す
    document.onkeydown = null;
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
    saveMemos(){
      //memosの配列をfirebaseのRealtimeDBに保存する
      firebase
        .database()
        .ref('memos/'+this.user.uid)
        .set(this.memos);
      alert('データをセーブしました')      
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
    margin: 10px auto 0;
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
    margin-top: 10px;
  }
  .memoListWrapper{
    width: 19%;
    border-top: 1px solid #ccc;
  }
  .memoList{
    padding: 10px;
    box-sizing: border-box;
    text-align: left;
    border-bottom: 1px solid #ccc;
    border-left: 1px solid #ccc;
    border-right: 1px solid #ccc;
    &:nth-child(even){
      background: #ccc;
    }
    &[data-selected="true"]{
      background: #ccf;
      color: #fff;
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
  .deleteMemoBtn{
    margin-top: 20px;
  }
  .saveMemosBtn{
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
