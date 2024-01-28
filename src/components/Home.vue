<script >
export default {
  data() {
    return {
      sth: '',
      conversationHistory:[]

    }

  },
  methods:{
    ask(){
      if(this.sth===""){
        return;
      }
      this.conversationHistory.push(this.sth)
      this.sth=''
    },
    clear(){
      this.conversationHistory=[]
    }
  }
}
</script>

<!-- 待办事件-->
<template>
  <ul>
    <li>
<!-- 输入框 -->
  <input v-model="sth" @keyup.enter="ask"  placeholder="请说话" id="inputbar">
<!--  输入按钮-->
  <button @click="ask"  id="inputbtn">发送</button>
    </li>

  <!--  展示框-->
  <li v-for="(item, index) in this.conversationHistory" class="mainli"> {{index+1}} . {{item}}</li>
  <!--  尾部-->
  <li  id="last"> 总计 {{this.conversationHistory.length}} 项 <a @click="clear">清空</a></li>
</ul>
</template>

<style >



#inputbtn{
  color: green;
  width: 100px;
  background-color: aqua;
}

#inputbar{
  width: 300px;
}

.mainli{

  color: white;
  background-color: orange;
  text-align: left;
  padding-left: 50px; /* 左侧保留20px的空白 */
}
li{
  width: 500px;
  list-style-type: none; /* 取消前面的点 */

}
#last{
  text-align: right;
  padding-right: 50px;
}
</style>