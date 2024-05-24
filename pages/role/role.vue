当然，以下是您的完整代码：

<template>
  <view>
    带上下文问题(可修改)：<input v-model="TEXT" style="border: 1px solid gainsboro;" />
    <button style="background-color: blue;color: white;" @click="sendToSpark()">发送给大模型</button>
    <text style="width: 90%;flex-wrap: wrap;">{{ sparkResult }}</text>
  </view>
</template>

<script>
import * as base64 from "base-64";
import CryptoJS from '../../static/crypto-js/crypto-js.js';
import parser from '../../static/fast-xml-parser/src/parser';
import * as utf8 from "utf8";
import URL from 'url';

export default {
  data() {
    return {
      TEXT: '你好，我的名字叫大王',
      httpUrl: "https://spark-api.xf-yun.com/v3.5/chat",
      modelDomain: '',
      APPID: '8203cacb',
      APISecret: 'YTE4MjM3NWIwYzZkOTUxZWM2ZmY5NDFj',
      APIKey: '3e21246e8e7a82a4d73233bc7a929dc9',
      sparkResult: '',
      historyTextList: [],
      tempRes: '',
      socketTask: null,
      wsLiveFlag: false
    };
  },
  methods: {
    async createCharacter() {
      try {
        const appId = this.APPID;
        const playerId = '123456';
        const agentName = '小明';
        const agentType = '学生';
        const description = '好奇，笨，刨根问底，爱提问';
        const identity = '学生';
        const personalityDescription = '好奇，笨，刨根问底，爱提问';

        const languageStyle = [
          {
            scene: "学生在课堂上提问",
            example: "这个问题我不太明白，能再解释一下吗？",
            field: "1"
          },
          {
            scene: "学生与同学讨论",
            example: "我觉得这个实验结果很有趣，你怎么看？",
            field: "2"
          }
        ];

        const hobby = '阅读';
        const speaker = 'vcn';
        const keyPersonality = '乐观';
        const mission = '探索世界，永不停止学习';

        const data = {
          appId,
          playerId,
          agentName,
          agentType,
          description,
          identity,
          personalityDescription,
          languageStyle,
          hobby,
          speaker,
          keyPersonality,
          mission
        };

        const response = await uni.request({
          url: 'https://ai-character.xfyun.cn/api/open/agent/edit-character',
          method: 'POST',
          data: JSON.stringify(data),
          dataType: 'json',
          header: {
            'Content-Type': 'application/json'
          }
        });

        console.log('创建人格成功', response[1].data);
      } catch (error) {
        console.error('请求发生错误:', error);
      }
    },

    async sendToSpark() {
      let myUrl = await this.getWebSocketUrl();
      this.tempRes = "";
      let realThis = this;

      this.socketTask = uni.connectSocket({
        url: myUrl,
        method: 'GET',
        success: res => {
          console.log(res, "ws成功连接...", myUrl);
          realThis.wsLiveFlag = true;
        }
      });

      realThis.socketTask.onError((res) => {
        console.log("连接发生错误，请检查appid是否填写", res);
      });

      realThis.socketTask.onOpen((res) => {
        this.historyTextList.push({
          "role": "user",
          "content": this.TEXT
        });
        console.info("wss的onOpen成功执行...", res);

        let params = {
          "header": {
            "app_id": this.APPID,
            "uid": "aef9f963-7"
          },
          "parameter": {
            "chat": {
              "domain": this.modelDomain,
              "temperature": 0.5,
              "max_tokens": 1024
            }
          },
          "payload": {
            "message": {
              "text": this.historyTextList,
              "roles": [
                {
                  "role": "student",
                  "identity": "学生",
                  "description": "好奇，笨，刨根问底，爱提问"
                }
              ]
            }
          }
        };

        console.log("请求的params：" + JSON.stringify(params));
        this.sparkResult = this.sparkResult + "\r\n我：" + this.TEXT + "\r\n";
        this.sparkResult = this.sparkResult + "大模型：";
        console.log("发送第一帧...", params);
        realThis.socketTask.send({
          data: JSON.stringify(params),
          success() {
            console.log('第一帧发送成功');
          }
        });
      });

      realThis.socketTask.onMessage((res) => {
        console.log('收到API返回的内容：', res.data);
        let obj;
        try {
          obj = JSON.parse(res.data);
        } catch (e) {
          console.error('JSON 解析错误:', e);
          return;
        }

        if (obj.payload && obj.payload.choices) {
          let dataArray = obj.payload.choices.text;
          for (let i = 0; i < dataArray.length; i++) {
            realThis.sparkResult = realThis.sparkResult + dataArray[i].content;
            realThis.tempRes = realThis.tempRes + dataArray[i].content;
          }
        } else {
          console.warn('未找到 payload 或 choices 属性:', obj);
        }

        let temp = obj;
        if (temp.header.code !== 0) {
          console.log(`${temp.header.code}:${temp.message}`);
          realThis.socketTask.close({
            success(res) {
              console.log('关闭成功', res);
              realThis.wsLiveFlag = false;
            },
            fail(err) {
              console.log('关闭失败', err);
            }
          });
        }
        if (temp.header.code === 0 && temp.header.status === 2) {
          realThis.sparkResult = realThis.sparkResult + "\r\n**********************************************";
          this.historyTextList.push({
            "role": "assistant",
            "content": this.tempRes
          });
          setTimeout(() => {
            realThis.socketTask.close({
              success(res) {
                console.log('关闭成功', res);
              },
              fail(err) {
                console.log('关闭失败', err);
              }
            });
          }, 1000);
        }
      });
    },

    getWebSocketUrl() {
      console.log(this.httpUrl);
      var httpUrlHost = (this.httpUrl).substring(8, this.httpUrl.indexOf('/', 8));
      var httpUrlPath = (this.httpUrl).substring(this.httpUrl.indexOf('/', 8));
      console.log(httpUrlHost);
      console.log(httpUrlPath);

      switch (httpUrlPath) {
        case "/v1.1/chat":
          this.modelDomain = "general";
          break;
        case "/v2.1/chat":
          this.modelDomain = "generalv2";
          break;
        case "/v3        .1/chat":
          this.modelDomain = "generalv3";
          break;
        case "/v3.5/chat":
          this.modelDomain = "generalv3.5";
          break;
      }
      console.log(this.modelDomain);

      return new Promise((resolve, reject) => {
        var url = "wss://" + httpUrlHost + httpUrlPath;
        var host = "spark-api.xf-yun.com";
        var apiKeyName = "api_key";
        var date = new Date().toGMTString();
        var algorithm = "hmac-sha256";
        var headers = "host date request-line";
        var signatureOrigin = `host: ${host}\ndate: ${date}\nGET ${httpUrlPath} HTTP/1.1`;
        var signatureSha = CryptoJS.HmacSHA256(signatureOrigin, this.APISecret);
        var signature = CryptoJS.enc.Base64.stringify(signatureSha);
        var authorizationOrigin = `${apiKeyName}="${this.APIKey}", algorithm="${algorithm}", headers="${headers}", signature="${signature}"`;
        var authorization = base64.encode(authorizationOrigin);
        url = `${url}?authorization=${authorization}&date=${encodeURI(date)}&host=${host}`;

        resolve(url); // 主要是返回地址
      });
    },
  },
 async respondToConversation() {
   try {
     const params = {
       header: {
         app_id: "8203cacb",
         uid: "aef9f963-7",
         agent_id: "513fb8e354a546e75c0c7bda32a408fd"
       },
       parameter: {
         chat: {
           chat_id: "test1234567",
           pre_chat_id: "test123456"
         }
       },
       payload: {
         message: {
           text: [
             {
               role: "老师",
               content: "同学你好"
             }
           ]
         }
       }
     };
 
     const response = await uni.request({
       url: 'wss://ai-character.xfyun.cn/api/open/interactivews/{sid}?appId=xxx&timestamp=xxx&signature=xxxxxx',
       method: 'POST',
       data: JSON.stringify(params),
       header: {
         'Content-Type': 'application/json'
       }
     });
 
     console.log('对话响应成功', response);
   } catch (error) {
     console.error('对话响应发生错误:', error);
   }
 }
,
  created() {
      // 在页面加载时创建角色
      this.createCharacter();
    },

  mounted() {
    // 调用函数启动WebSocket连接
    this.sendToSpark();
    // 在页面加载时执行对话响应
    this.respondToConversation();
  }

}
</script>

<style>
</style>
