<template>
  <div class="hello">
    <!-- ... 省略其他代码 ... -->

    <!-- 添加一个输入框用于输入用户请求 -->
    <input type="text" v-model="userInput" placeholder="输入您的请求">

    <!-- 添加一个按钮来触发请求 -->
    <button @click="sendRequest">发送文心一言请求</button>
    <button @click="sendRequest2">发送GPT请求</button>

    <!-- 可以在这里显示响应结果 -->
    <div v-if="response">
      <h3>百度响应结果:</h3>
      <pre>{{ response.result }}</pre>
    </div>
    <div v-if="response2">
      <h3>GPT响应结果:</h3>
      <pre>{{ response2.choices[0].message.content }}</pre>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      userInput: "", // 用户输入的数据
      response: null, // 用于存储响应数据
      response2: null, // 用于存储响应数据
    };
  },
  methods: {
    async sendRequest() {
      try {
        const res = await axios.post("http://localhost:3000/api/baidu-query", {
          userInput: this.userInput, // 使用绑定的用户输入
        }, {
          timeout: 30000 // 设置请求超时时间为 30 秒
        });
        this.response = res.data;
      } catch (error) {
        console.error("请求错误:", error);
        this.response = error.message; // 显示具体的错误信息
      }
    },
    async sendRequest2() {
      try {
        const res = await axios.post("http://localhost:3000/api/gpt-query", {
          userInput: this.userInput, // 使用绑定的用户输入
        }, {
          timeout: 30000 // 设置请求超时时间为 30 秒
        });
        this.response2 = res.data;
      } catch (error) {
        console.error("请求错误:", error);
        this.response2 = error.message; // 显示具体的错误信息
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
input[type="text"] {
  margin: 20px 0;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  background-color: #42b983;
  color: white;
  cursor: pointer;
}
button:hover {
  background-color: #333;
}
</style>
