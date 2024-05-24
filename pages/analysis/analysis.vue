<template>
  <div class="container">
    <navBar titleName="成绩分析" />
    <div v-if="loading" class="loading">加载中...</div>

    <!-- Chart 1 Container -->
    <div class="chart-container">
      <view class="T1">
        成绩变化图
      </view>
      <view class="charts-box">
        <data_charts type="line" :chartData="echartsData" />
      </view>
    </div>

    <!-- Chart 2 Container -->
    <div class="chart-container">
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
      <img src="https://i.postimg.cc/44bTPxrv/image.png" alt="Popup Image"
        style="width: 100%; max-height: 150px; object-fit: cover; border-radius: 10px 10px 0 0;">

      <form @submit="handleSubmit">


        <label class="subject">选择学科</label>

        <div style="margin-bottom: 10px; display: flex; align-items: center;">
          <label class="score" style="margin-right: 10px;">填写学科:</label>
          <input type="text" id="subject" v-model="subject"
            style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
        </div>


        <div style="margin-bottom: 10px; display: flex; align-items: center;">
          <label class="score" style="margin-right: 10px;">填写成绩:</label>
          <input type="number" id="score" v-model="score" placeholder="成绩"
            style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
        </div>

        <div style="margin-bottom: 10px; display: flex; align-items: center;">
          <label class="examDate" style="margin-right: 10px;">选择考试日期:</label>
          <input type="date" id="examDate" v-model="examDate" placeholder="日期"
            style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
        </div>

        <div style="display: flex; justify-content: space-between;">
          <button type="button" @click="handleReset" class="round-btn">重置</button>
          <button type="submit" @click="handleSubmit" class="round-btn">提交</button>
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
export default {
  components: {
    data_charts
  },
  data() {
    return {
     echartsData: {
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
    };
  },
  mounted() { //钩子函数 页面加载时自动执行
    // 发送请求获取数据
    this.getData();
  },
  methods: {
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
          this.echartsData = JSON.parse(JSON.stringify(Linechart));
          console.log(this.echartsData);

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
                name: "成交量2",
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
          const seriesIndex = this.chartData.series.findIndex(series => series.name === formData.subject);
          if (seriesIndex !== -1) {
            const formattedDate = examDate.toISOString().slice(0, 10);
            this.chartData.categories.push(formattedDate);
            this.chartData.series[seriesIndex].data.push(formData.score);
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
  }
};
</script>









<style scoped>
.container {
  background-color: #eafdff;
}

/* Add blue border to chart containers */
.chart-container {
  border: 2px solid white;
  /* Blue border color */
  border-radius: 10px;
  /* Optional: Rounded border corners */
  margin-bottom: 20px;
  /* Optional: Add some space between containers */
  margin: 30px;
  background-color: white;
  background-image: linear-gradient(to bottom, #d5f8ff, white 30%);
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
  background-color: #aae8f6;
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

.round-btn {
  width: 80px;
  height: 40px;
  border-radius: 20px;
  cursor: pointer;
  background-color: #5187FC;
  color: white;
  border: none;
}

.round-btn:hover {
  background-color: #24538b;
}


.score,
.examDate,
.subject {
  font-size: 18px;
}
</style>