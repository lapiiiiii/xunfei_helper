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
        <qiun-data-charts type="line" :chartData="chartData" />
      </view>
    </div>

    <!-- Chart 2 Container -->
    <div class="chart-container">
      <view class="T1">
        学科优劣图
      </view>
      <view class="radar-chart-box">
        <qiun-data-charts type="radar" :chartData="radarChartData" />
      </view>
    </div>

    <!-- Circle Button -->
    <div class="circle-btn" @click="showPopup = !showPopup">
      <text class="plus">+</text>
    </div>

    <!-- Popup -->
    <div class="popup" v-show="showPopup">
      <!-- Add Image -->
      <img src="https://i.postimg.cc/44bTPxrv/image.png" alt="Popup Image" style="width: 100%; max-height: 150px; object-fit: cover; border-radius: 10px 10px 0 0;">

      <form @submit="handleSubmit">
        <div style="margin-bottom: 10px;">
          <label class="subject" style="margin-right: 20px;">选择学科</label>
          <select id="subject" style="width: 60px;height:40px;" v-model="subject">
            <option value="语文">语文</option>
            <option value="数学">数学</option>
            <option value="英语">英语</option>
            <option value="物理">物理</option>
            <option value="化学">化学</option>
            <option value="生物">生物</option>
          </select>
        </div>

        <div style="margin-bottom: 10px; display: flex; align-items: center;">
          <label class="score" style="margin-right: 10px;">填写成绩:</label>
          <input type="number" id="score" v-model="score" placeholder="成绩" style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
        </div>
        
        <div style="margin-bottom: 10px; display: flex; align-items: center;">
          <label class="examDate" style="margin-right: 10px;">选择考试日期:</label>
          <input type="date" id="examDate" v-model="examDate" placeholder="日期" style="border: 1px solid #ccc; border-radius: 5px; padding: 5px;">
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
export default {
  data() {
    return {
      chartData: {
        categories: [],
        series: []
      },
      radarChartData: {
        categories: ["语文", "数学", "英语", "物理", "化学", "生物"],
        series: [{ name: "成绩", data: [] }]
      },
      loading: true, // 控制加载提示的显示
      showPopup: false,
      subject: "",
      score: "",
      examDate: "",
      suggestions: [] // 建议数组
    };
  },
  mounted() {
    // 发送请求获取数据
    uni.request({
      url: '你的网页地址',
      success: (res) => {
        // 更新数据
        this.chartData.categories = res.data.categories;
        this.chartData.series = res.data.series;
        // 更新雷达图数据
        const averages = res.data.series.map(subject => {
          const average = subject.data.reduce((acc, val) => acc + val, 0) / subject.data.length;
          return average;
        });
        this.radarChartData.series[0].data = averages; // 更新雷达图数据

        // 更新建议
        this.suggestions = res.data.suggestions;

        // 加载完成，隐藏加载提示
        this.loading = false;
      },
      fail: (err) => {
        console.error('请求失败：', err);
        // 加载失败，隐藏加载提示
        this.loading = false;
      }
    });
  },
  methods: {
    handleSubmit() {
      // 获取表单数据
      const subject = this.subject;
      const score = this.score;
      const examDate = this.examDate;

      // 把表单数据发送到网页
      uni.request({
        url: '你的网页地址',
        data: {
          subject,
          score,
          examDate
        },
        success: (res) => {
          this.chartData.categories = res.data.categories;
          this.chartData.series = res.data.series;
          // 更新雷达图数据
          const averages = res.data.series.map(subject => {
            const average = subject.data.reduce((acc, val) => acc + val, 0) / subject.data.length;
            return average;
          });
          this.radarChartData.series[0].data = averages; // 更新雷达图数据

          // 更新建议
          this.suggestions = res.data.suggestions;

          // 加载完成，隐藏加载提示
          this.loading = false;
        },
        fail: (err) => {
          console.error('请求失败：', err);
          // 加载失败，隐藏加载提示
          this.loading = false;
        }
      });
    },
    handleReset() {
      this.subject = '';
      this.score = '';
      this.examDate = '';
    }
  }
};
</script>







<style scoped>
.container {
  background-color: #eafdff;
}

/* Add blue border to chart containers */
.chart-container {
  border: 2px solid white; /* Blue border color */
  border-radius: 10px; /* Optional: Rounded border corners */
  margin-bottom: 20px; /* Optional: Add some space between containers */
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
.subject
{
	font-size:18px;
}



</style>