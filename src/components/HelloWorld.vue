<template>
  <div class="hello">
    <!-- 添加一个输入框用于输入用户请求 -->
    <input type="text" v-model="userInput" placeholder="输入您的请求" />

    <!-- 添加一个按钮来触发请求 -->
    <button @click="sendBaiduRequest">发送文心一言请求</button>
    <button @click="sendGPTRequest">发送GPT请求</button>

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
        const res = await axios.post(
          "http://localhost:3000/api/baidu-query",
          {
            userInput: this.userInput, // 使用绑定的用户输入
          },
          {
            timeout: 30000, // 设置请求超时时间为 30 秒
          }
        );
        this.response = res.data;
      } catch (error) {
        console.error("请求错误:", error);
        this.response = error.message; // 显示具体的错误信息
      }
    },
    async sendRequest2() {
      try {
        const res = await axios.post(
          "http://localhost:3000/api/gpt-query",
          {
            userInput: this.userInput, // 使用绑定的用户输入
          },
          {
            timeout: 30000, // 设置请求超时时间为 30 秒
          }
        );
        this.response2 = res.data;
      } catch (error) {
        console.error("请求错误:", error);
        this.response2 = error.message; // 显示具体的错误信息
      }
    },
    async sendGPTRequest() {
      const OPENAI_API_KEY = "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"; // 需要将密钥存储在前端，但这是不安全的
      try {
        const response = await axios.post(
          "https://api.openai.com/v1/chat/completions",
          {
            model: "gpt-3.5-turbo",
            messages: [{ role: "user", content: this.userInput }],
            temperature: 0.7,
          },
          {
            headers: {
              "Content-Type": "application/json",
              Authorization: `Bearer ${OPENAI_API_KEY}`,
            },
          }
        );

        this.response2 = response.data;
      } catch (error) {
        console.error("请求 GPT API 出错:", error);
        this.response2 = error.message;
      }
    },
    async sendBaiduRequest() {
      const BAIDU_ACCESS_TOKEN = "24.b06e7f8b4f98a2330fbaf8c43e704e7a.2592000.1705762138.282335-45463185"; // 同样需要在前端存储，存在安全风险
      try {
        const response = await axios.post(
          "https://aip.baidubce.com/rpc/2.0/ai_custom/v1/wenxinworkshop/chat/completions_pro?access_token=" +
            BAIDU_ACCESS_TOKEN,
          {
            messages: [
              {
                role: "user",
                content: this.userInput,
              },
            ],
          },
          {
            headers: {
              "Content-Type": "application/json",
            },
          }
        );

        this.response = response.data;
      } catch (error) {
        console.error("请求百度 API 出错:", error);
        this.response = error.message;
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