<template>
  <div>
    <div v-for="(message,index) in messages" :key="index" :class="{ 'user-message': message.role==='user', 'ai-message': message.role!=='user' }">
      {{ message.content }}
    </div>
    <input v-model="userInput" @keyup.enter="sendMessage" placeholder="输入消息..." />
    <input v-model="api_key" @change="save_apikey"  type="password" placeholder="输入apikey" />
     <select v-model="sel_modle" :value="sel_modle" @change="change_modle">选择你要用的引擎
      <option v-for="(key,value) in modle" :key="key" :value="value" >{{key}}</option>
    </select>
    <p>错误信息</p>
    <li v-for="(msg,index) in errormsg" :key="index">{{index +1}} . {{msg}}</li>

  </div>
</template>

<script>
export default {
  data() {
    return {
      userInput: '',
      // messages: [ { role: 'system', content: 'You are a helpful assistant.' }],
      messages: [],
      errormsg:[],
      modle:{
        'gpt-4':'gpt-4',
        'gpt-3.5-turbo':'gpt-3.5-turbo'
      },
      sel_modle:"",
      api_key:'',

    };
  },
  created(){

    this.api_key = localStorage.getItem('apikey') ? localStorage.getItem('apikey') : '';
    this.sel_modle = localStorage.getItem('sel_modle') ? localStorage.getItem('sel_modle') : 'gpt-3.5-turbo';

  }
  ,
  methods: {
    save_apikey(){
      if(this.api_key === ""){
        return;
      }
      localStorage.setItem('apikey', this.api_key);

    },
    change_modle(){
      localStorage.setItem('sel_modle', this.sel_modle);
    },

    async sendMessage() {
      // 用户发送消息
      // this.messages.push({ id: Date.now(), text: this.userInput, user: true });
      this.messages.push({ role:'user', content: this.userInput,});
      this.userInput = '';

      // 发送请求
      const aiReply = await this.sendRequest();

      // AI回复
      // this.messages.push({ id: Date.now(), text: aiReply, user: false });
      this.messages.push({  role:aiReply.role, content:  aiReply.content.trim() });
    },
    async sendRequest() {
      try {
        const response = await fetch('https://api.openai.com/v1/chat/completions', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer '+ this.api_key , // 替换为你的API密钥
          },
          body: JSON.stringify({
            // model: 'gpt-4-1106-preview',
            model:this.sel_modle,
            // prompt: this.getConversationHistory(),
            messages:  this.messages,
            max_tokens: 250,
            n: 1,
            stop: null,
            temperature: 0.7,
          }),

        });

        const data = await response.json();
        console.log(data)
        console.log(this.messages)
        return data.choices[0].message;
      } catch (error) {
        console.error('Error sending request to GPT API:', error.message);
        this.errormsg.unshift(error.message)
        return '抱歉，发生了错误。';
      }
    },

  },
};
</script>

<style scoped>
.user-message {
  //text-align: right;
  //margin-bottom: 8px;
}

.ai-message {
  //text-align: left;
  //margin-bottom: 8px;
}
</style>
