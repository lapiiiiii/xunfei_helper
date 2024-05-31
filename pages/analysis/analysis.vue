<template>
  <div class="container">
    <view class="top-section">
      <text class="welcome-text">
        <image src="../../static/index/讯飞星火.png" style="width:20px;height:20px;top:4px;margin-right:7px" />背诵助手
      </text>
    </view>
    <div v-if="loading" class="loading">加载中...</div>

    <view class="page-container">
      <!-- 可点击的图片 -->


      <view class="right-icon-wrapper" @click="toggleDrawer(); sendToSpark()">
        <image src="../../static/analysis/燕尾形.png" class="right-icon-image" />
        <text class="right-icon-text">建议</text>
      </view>

      <!-- 从右侧划出的抽屉页面 -->
      <view :class="['drawer', { 'open': drawerOpen }]">
        <!-- 内容区域 -->
        <view class="title">
          <image src="../../static/index/讯飞星火.png" class="title-icon" /> <!-- 新增图片 -->
          学习建议
        </view>

        <view class="content">
          {{ scoreSuggestion }}
        </view>
        <!-- 收起按钮 -->
        <view class="close-btn" @click="toggleDrawer">收起</view>
      </view>
    </view>
    <!-- Chart 1 Container -->
    <!-- Chart 1 Container -->
    <div class="chart-container" v-if="chartsData.series.length">
      <view class="T1">
        成绩变化图
      </view>
      <view class="charts-box">
        <data_charts type="line" :chartData="chartsData" />
      </view>
    </div>

    <!-- Chart 2 Container -->
    <div class="chart-container" v-if="radarChartData.series.length">
      <view class="T1">
        学科优劣图
      </view>
      <view class="radar-chart-box">
        <data_charts type="radar" :chartData="radarChartData" />
      </view>
    </div>


    <!-- Circle Button -->
    <div class="circle-btn" @click="showPopup = !showPopup">
      <text class="plus">+</text>
    </div>

    <!-- Popup -->
    <div class="popup" v-show="showPopup">
      <!-- Add Image -->
      <image src="../../static/analysis/post.png" alt="Popup Image"
        style="width: 100%; max-height: 120px;  border-radius: 10px 10px 0 0;" />

      <form @submit="handleSubmit">


        <div style="margin-bottom: 10px; display: flex; align-items: center;margin-top:10px;">
          <label class="score" style="margin-right: 10px;">填写学科:</label>
          <input type="text" id="subject" v-model="subject" placeholder="请输入学科"
            style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
        </div>


        <div style="margin-bottom: 10px; display: flex; align-items: center;">
          <label class="score" style="margin-right: 10px;">填写成绩:</label>
          <input type="number" id="score" v-model="score" placeholder="请输入成绩"
            style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
        </div>

        <div style="margin-bottom: 10px; display: flex; align-items: center;">
          <label class="examDate" style="margin-right: 10px;">选择考试日期:</label>
          <input type="date" id="examDate" v-model="examDate" placeholder="日期"
            style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
        </div>

        <div style="display: flex; justify-content: space-between;">
          <button type="button" @click="handleReset" class="round-btn1">重置</button>
          <button type="submit" @click="handleSubmit" class="round-btn2">提交</button>
        </div>
      </form>
    </div>

    <!-- Container for Suggestions -->
    <div class="suggestion-container">
      <ul>
        <li v-for="(suggestion, index) in suggestions" :key="index">{{ suggestion }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import { baseUrl } from '@/config.js';
import data_charts from '@/components/qiun-data-charts/components/qiun-data-charts/qiun-data-charts';
import * as base64 from "base-64";
import CryptoJS from "crypto-js";
export default {
  components: {
    data_charts
  },
  data() {
    return {
      TEXT: '请分析我发给你的成绩信息，然后给出能够改进我的学习的建议',
      httpUrl: "https://spark-api.xf-yun.com/v3.5/chat",
      modelDomain: '',
      APPID: '8203cacb',
      APISecret: 'YTE4MjM3NWIwYzZkOTUxZWM2ZmY5NDFj',
      APIKey: '3e21246e8e7a82a4d73233bc7a929dc9',
      sparkResult: '',
      historyTextList: [],
      tempRes: '',
      scoreSuggestion: '发生了某些错误，无法获取建议',
      chartsData: {
        categories: [],
        series: []
      },
      radarChartData: {
        categories: [],
        series: []
      },
      loading: true, // 控制加载提示的显示
      showPopup: false,
      subject: "",
      score: "",
      examDate: "",
      suggestions: [], // 建议数组
      username: "testUser",
      drawerOpen: false
    };
  },
  mounted() { //钩子函数 页面加载时自动执行
    // 发送请求获取数据
    this.getData();
  },
  methods: {
    toggleDrawer() {
      this.drawerOpen = !this.drawerOpen;
    },
    getData() {
      uni.request({
        url: baseUrl + "/score/get",
        method: "GET",
        data: {
          username: this.username
        },
        success: (res) => {
          console.log(res); // 查看 res 的内容
          // 提取时间、成绩和学科
          const data = res.data;
          const scoresBySubject = data.reduce((result, item) => {
            if (!result[item.subject]) {
              result[item.subject] = [];
            }
            result[item.subject].push({ score: item.score, time: item.time });
            return result;
          }, {});
          console.log(scoresBySubject);
          const xAxisData = [];
          const seriesData = [];
          for (const subject in scoresBySubject) {
            const series = {
              name: subject,
              data: []
            };
            for (const item of scoresBySubject[subject]) {
              const timeIndex = xAxisData.indexOf(item.time);
              if (timeIndex === -1) {
                xAxisData.push(item.time);
                series.data.push(item.score);
              } else {
                series.data[timeIndex] = item.score;
              }
            }
            seriesData.push(series);
          }

          let Linechart = {
            categories: xAxisData,
            series: seriesData
          };
          this.chartsData = JSON.parse(JSON.stringify(Linechart));
          console.log(this.chartsData);

          // 计算各学科的平均成绩并更新到雷达图数据中
          let subjects = seriesData.map(series => series.name);
          let averageScores = seriesData.map(series => {
            let sum = series.data.reduce((a, b) => a + b, 0);
            let avg = sum / series.data.length;
            return avg;
          });

          let radarchart = {
            categories: subjects,
            series: [
              {
                name: "成绩",
                data: averageScores
              }
            ]
          };
          this.radarChartData = JSON.parse(JSON.stringify(radarchart));
          console.log(this.radarChartData);

          // 更新页面
          this.loading = false;
        },
        fail: (err) => {
          console.error('请求数据失败', err);
          // Handle request failure
        }
      });
    },

    handleReset() {
      this.subject = "";
      this.score = "";
      this.examDate = "";
    },
    handleSubmit() {
      const subject = this.subject;
      const score = this.score;
      const examDate = new Date(this.examDate);
      const isoDatestring = examDate.toISOString(); // 修改此行
      const formattedDatestring = isoDatestring.slice(0, 23) + "+00:00";
      const formData = {
        username: this.username,
        subject: subject,
        score: score,
        time: formattedDatestring
      };

      const formDataString = JSON.stringify(formData);
      uni.request({
        url: baseUrl + '/score/add',
        method: 'POST',
        data: formDataString, // 直接传输表单数据对象
        header: {
          'content-type': 'application/json'
        },
        success: (res) => {
          console.log('添加成绩成功', res);
          // 将新数据添加到折线统计图的数据中
          const seriesIndex = this.chartsData.series.findIndex(series => series.name === formData.subject);
          if (seriesIndex !== -1) {
            const formattedDate = examDate.toISOString().slice(0, 10);
            this.chartsData.categories.push(formattedDate);
            this.chartsData.series[seriesIndex].data.push(formData.score);
          } else {
            console.error('找不到相应的学科系列');
          }
          // 将新数据添加到雷达图的数据中
          const subjectIndex = this.radarChartData.categories.indexOf(formData.subject);
          this.radarChartData.series[0].data[subjectIndex] = formData.score;

          // 更新页面
          // 这里你可以调用一个方法来更新页面，或者直接写更新逻辑
          // 这里只是一个示例，具体实现需要根据你的项目结构来调整
          this.getData(); // 重新获取数据
          this.showPopup = false;
        },
        fail: (err) => {
          console.error('添加成绩失败', err);
        }
      });
    },

    //讯飞接口调用
    async sendToSpark() {
				let myUrl = await this.getWebSocketUrl();
				this.tempRes = "";
				let realThis = this;
				this.socketTask = uni.connectSocket({
					url: myUrl,
					method: 'GET',
					success: res => {
						console.log(res, "ws成功连接...", myUrl)
						realThis.wsLiveFlag = true;
					}
				})
				realThis.socketTask.onError((res) => {
					console.log("连接发生错误，请检查appid是否填写", res)
				})
				realThis.socketTask.onOpen((res) => {
					this.historyTextList.push({
						"role": "user",
						"content": this.TEXT+JSON.stringify(this.chartsData)+JSON.stringify(this.radarChartData)
					})
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
								"text": this.historyTextList
							}
						}
					};
					console.log("请求的params：" + JSON.stringify(params))
					this.sparkResult = this.sparkResult + "\r\n我：" + this.TEXT + "\r\n"
					this.sparkResult = this.sparkResult + "大模型："
					console.log("发送第一帧...", params)
					realThis.socketTask.send({ // 发送消息，，都用uni的官方版本
						data: JSON.stringify(params),
						success() {
							console.log('第一帧发送成功')
						}
					});
				});

				// 接受到消息时
				realThis.socketTask.onMessage((res) => {
					console.log('收到API返回的内容：', res.data);
					let obj = JSON.parse(res.data)
					// console.log("我打印的"+obj.payload);
					let dataArray = obj.payload.choices.text;
					for (let i = 0; i < dataArray.length; i++) {
						realThis.sparkResult = realThis.sparkResult + dataArray[i].content
						realThis.tempRes = realThis.tempRes + dataArray[i].content
					}
          this.scoreSuggestion = realThis.tempRes;

					// realThis.sparkResult =realThis.sparkResult+ 
					let temp = JSON.parse(res.data)
					// console.log("0726",temp.header.code)
					if (temp.header.code !== 0) {
						console.log(`${temp.header.code}:${temp.message}`);
						realThis.socketTask.close({
							success(res) {
								console.log('关闭成功', res)
								realThis.wsLiveFlag = false;
							},
							fail(err) {
								console.log('关闭失败', err)
							}
						})
					}
					if (temp.header.code === 0) {
						if (res.data && temp.header.status === 2) {
							realThis.sparkResult = realThis.sparkResult +
								"\r\n**********************************************"
							this.historyTextList.push({
								"role": "assistant",
								"content": this.tempRes
							})
							setTimeout(() => {
								realThis.socketTask.close({
									success(res) {
										console.log('关闭成功', res)
									},
									fail(err) {
										// console.log('关闭失败', err)
									}
								})
							}, 1000)
						}
					}
				})
			},
      getWebSocketUrl() {
				console.log(this.httpUrl)
				var httpUrlHost = (this.httpUrl).substring(8, 28);
				var httpUrlPath = (this.httpUrl).substring(28);
				console.log(httpUrlHost)
				console.log(httpUrlPath)
				switch (httpUrlPath) {
					case "/v1.1/chat":
						this.modelDomain = "general";
						break;
					case "/v2.1/chat":
						this.modelDomain = "generalv2";
						break;
					case "/v3.1/chat":
						this.modelDomain = "generalv3";
						break;
					case "/v3.5/chat":
						this.modelDomain = "generalv3.5";
						break;
				}
				console.log(this.modelDomain)
				return new Promise((resolve, reject) => {
					var url = "wss://"+httpUrlHost+httpUrlPath;
					var host = "spark-api.xf-yun.com";
					var apiKeyName = "api_key";
					var date = new Date().toGMTString();
					var algorithm = "hmac-sha256";
					var headers = "host date request-line";
					var signatureOrigin = `host: ${host}\ndate: ${date}\nGET ${httpUrlPath} HTTP/1.1`;
					var signatureSha = CryptoJS.HmacSHA256(signatureOrigin, this.APISecret);
					var signature = CryptoJS.enc.Base64.stringify(signatureSha);
					var authorizationOrigin =
						`${apiKeyName}="${this.APIKey}", algorithm="${algorithm}", headers="${headers}", signature="${signature}"`;
					var authorization = base64.encode(authorizationOrigin);
					url = `${url}?authorization=${authorization}&date=${encodeURI(date)}&host=${host}`;

					// console.log(url)
					resolve(url); // 主要是返回地址
				});
			},
  }
};
</script>





<style scoped>
.container {
  background-color: #e1eeff;
}

.top-section {
  background-color: #6993FF;
  height: 10%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0px 10px 10px rgba(0, 0, 0, 0.2);
  /* 添加阴影效果 */
  border-radius: 12px;
  /* 添加圆角边框 */
  display: flex;
  /* 将文本设置为 Flex 容器 */
  align-items: center;
  /* 垂直居中子元素 */
}

.welcome-text {
  color: #fff;
  font-size: 16px;

  padding-top: 30px;
  padding-bottom: 10px;
  text-align: center;
}

/* Add blue border to chart containers */
.chart-container {
  /* Blue border color */
  border-radius: 10px;
  /* Optional: Rounded border corners */
  margin-bottom: 20px;
  /* Optional: Add some space between containers */
  margin: 30px;
  background-color: white;
  background-image: linear-gradient(to bottom, #6993FF, white 30%);
}

.charts-box {
  width: 95%;
  height: 300px;
  position: relative;
}

.radar-chart-box {
  width: 95%;
  height: 300px;
  position: relative;
}

.T1 {
  font-size: 18px;
  color: #24538b;
  font-weight: bold;
  padding: 30px;
  margin-left: 70px;
  font-family: "微软雅黑";
}

.circle-btn {
  position: fixed;
  bottom: 30px;
  right: 42%;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: #6993FF;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

.plus {
  font-size: 24px;
  color: white;
}

.popup {
  position: fixed;
  bottom: 0;
  right: 0;
  left: 0;
  height: 50%;
  background-color: white;
  border-radius: 10px 10px 0 0;
  box-shadow: 0px -2px 4px rgba(0, 0, 0, 0.1);
  padding: 10px;
  overflow: auto;
}

form {
  display: flex;
  flex-direction: column;
}

input[type="number"],
input[type="date"] {
  margin-bottom: 10px;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.round-btn1 {
  width: 80px;
  height: 37px;
  border-radius: 20px;
  cursor: pointer;
  color: black;
  border: 1px solid gray;
  line-height: 37px;
}

.round-btn2 {
  width: 80px;
  height: 37px;
  border-radius: 20px;
  cursor: pointer;
  background-color: #5187FC;
  color: white;
  border: none;
  line-height: 37px;
}

.round-btn:hover {
  background-color: #24538b;
}


.score,
.examDate,
.subject {
  font-size: 18px;
}

.page-container {
  position: relative;
}

.right-icon-wrapper {
  position: fixed;
  top: 50px;
  right: 1px;
  width: 60px;
  /* 图标的宽度 */
  height: 60px;
  /* 图标的高度 */
  display: flex;
  align-items: center;
  justify-content: center;
  /* 水平和垂直居中 */
  cursor: pointer;
}

.right-icon-image {
  position: absolute;
  /* 绝对定位，覆盖整个容器 */
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.right-icon-text {
  position: absolute;
  /* 绝对定位，覆盖图片 */
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  color: fff;
  /* 文本颜色 */
  font-size: 17px;
  /* 文本大小 */
  text-align: right;
  /* 文本水平居中 */
  line-height: 60px;
  /* 使文本垂直居中 */
  font-weight: bold;
  text-shadow: 0 5px 5px rgba(255, 255, 255, 0.5);
  /* 添加阴影 */
}



.drawer {
  position: fixed;
  right: -100%;
  /* 初始隐藏在视图外 */
  top: 0;
  width: 100%;
  height: 100%;
  background-image: url("https://gd-hbimg.huaban.com/e02c70fe009f1ff9ce9f5dffb26ffeaf6a6a8cddb810-sfBIgh_fw658webp");
  transition: right 0.3s;
  background-size: 140% 100%;
  /* 拉伸图像以完全匹配元素的大小 */
  background-repeat: no-repeat;
  /* 不重复图像 */
  background-position: center;
  /* 图像居中显示 */
}

.drawer {
  position: fixed;
  right: -100%;
  /* 初始隐藏在视图外 */
  top: 0;
  width: 100%;
  height: 100%;
  background-color: white;
  transition: right 0.3s;
  z-index: 101;
  /* 确保在遮罩层上方 */
}

.drawer.open {
  right: 0;
  /* 划出 */
}


.title {
  font-size: 20px;
  text-align: center;
  margin-top: 50px;
  font-weight: bold;
  color: #5187FC;
  text-shadow: 2px 2px 4px rgba(197, 255, 252, 0.1);
  background-color: #f0f8ff;
  padding: 10px 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  width: 120px;
  display: flex;
  /* 使用Flex布局 */
  align-items: center;
  /* 垂直居中 */
  justify-content: center;
  /* 水平居中 */
  margin-left: 30%;
  margin-top: 15%;

}

.title-icon {
  width: 30px;
  /* 根据实际情况调整图标大小 */
  height: 30px;
  /* 根据实际情况调整图标大小 */
  margin-right: 10px;
  /* 在图标和文字之间添加一些空间 */
}


.content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  /* 垂直居中 */
  align-items: center;
  /* 水平居中 */
  padding: 52px;
  height: 100%;
  /* 设置高度为100%以支持垂直居中 */
  font-size: 15px;
  /* 字体大小设置为20px */
  color: #333;
  /* 可以调整字体颜色 */
  line-height: 40px;
  text-indent: 2em;
  margin-top: -105px;

}


.close-btn {
  position: absolute;
  right: 10px;
  bottom: 10px;
  padding: 10px;
  color: #5187FC;
  /* 按钮文本颜色 */
  font-size: 14px;
  /* 字体大小 */
  font-weight: bold;
  /* 字体加粗 */
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
  /* 更细致的文本阴影 */
  background-color: #f0f8ff;
  /* 淡蓝色背景 */
  border-radius: 10px;
  /* 圆角 */
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  /* 盒子阴影 */
  width: auto;
  /* 自动宽度 */
  min-width: 50px;
  /* 最小宽度 */
  display: inline-block;
  cursor: pointer;
  text-align: center;

}
</style>