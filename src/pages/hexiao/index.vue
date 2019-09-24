<template>
  <!-- <div class="bgf"> -->
    <div id="body">
      <div class="top" :style="{'top':topHeight}">
        <i-icon @click="GoBack" type="return" size="24" />
        <!-- <i-input title="<" type="number" placeholder="请输入收货人姓名" /> -->
        <input type="number" focus placeholder="请输入核销码" v-model="code" />
        <span @click="hexiao">核销</span>
      </div>

      <div class="border1px" :style="{'margin-top':margintopHeight}"></div>
      <div v-if="ifDiv" class="bod1">
        <img v-if="ifImg" src="../../../static/img/succ.png" alt="">
        <img v-else src="../../../static/img/tan.png" alt="">
        <span v-if="ifImg">核销成功</span>
        <span v-else>核销码不存在</span>
        <p @click="GoBack">返回拼团</p>
      </div>
      <div v-else class="bod2"></div>
      <!--<template v-for="rec in records">
    <div class="box" @click="GoToWriteoff">
      <p>{{rec.pname}}</p>
      <div>
        <span>{{rec.pname}}</span>
        <span>{{rec.code}}</span>
        <span>{{rec.price}}</span>
      </div>
    </div>
    <div class="border1px"></div>
      </template>-->
    </div>
  <!-- </div> -->
</template>
<script>
export default {
  data() {
    return {
      //   globalData: ""
      records: [],
      code: undefined,
      ifImg:true,
      ifDiv:false
    };
  }, 
  computed: {
    topHeight() {
      var aaa = "";
      wx.getSystemInfo({
        success: res => {
          aaa = res.statusBarHeight + 8 + "px";
        }
      });
      return aaa;
    },
    margintopHeight() {
      var bbb = "";
      wx.getSystemInfo({
        success: res => {
          bbb = res.statusBarHeight + 8 + 31 + "px";
        }
      });
      return bbb;
    }
  },
  onLoad() {
    this.ifDiv=false
    this.ifImg=true
  },
  methods: {
    hexiao() {
      var that=this
      this.$axios
        .post("routine/Store/writeCode", {
          sid: wx.getStorageSync("sid"),
          code: this.code
        })
        .then(function(response) {
          // wx.showToast({
          //   title: response.data.msg,
          //   icon: "none",
          //   duration: 1000
          // });
          if(response.data.msg=='核销码不存在'){
            that.ifDiv=true
            that.ifImg=false
          }else{
            that.ifDiv=true
            that.ifImg=true
          }
        });
    },
    GoBack() {
      wx.navigateBack({
        delta: 1
      });
    },
    GoToWriteoff() {
      wx.navigateTo({
        url: "/pages/Write-off/main"
      });
    }
  }
};
</script>

<style scoped>
.bod1{
  /* height: 100vh; */
  text-align: center;
}
.bod1 img{
  width: 78px;
  height: 78px;
  display: block;
  margin: 200px auto;
  margin-bottom: 0;
}
.bod1 span{
  font-size: 13px;
  line-height: 36px;
  color: #333333;
}
.bod1 p{
  width: 235px;
  height: 31px;
  display: block;
  background-color: #F35379;
  color: #ffffff;
  border-radius: 31px;
  font-size: 13px;
  text-align: center;
  line-height: 31px;
  margin: 20px auto;
}
.top {
  position: fixed;
  background-color: #ffffff;
}
.top i-icon {
  float: left;
  line-height: 25px;
}
.top input {
  float: left;
  font-size: 13px;
  width: 215px;
  height: 31px;
  margin-left: 10px;
  background-color: #F5F5F5;
  border-radius: 31px;
  padding-left: 15px;
}
.top span {
  color: #ffffff;
  font-size: 13px;
  position: absolute;
  left: 200px;
  top: 0px;
  z-index: 999999;
  width: 59px;
  height: 31px;
  line-height: 31px;
  background-color: #F35379;
  border-radius: 31px;
  text-align: center;
}
.box {
  width: 375px;
  height: 80px;
  color: #999999;
  font-size: 15px;
}
.box p {
  margin-top: 14px;
  margin-left: 15px;
}
.box div {
  padding: 0 15px 0px 15px;
  display: flex;
  justify-content: space-between;
}
.box div span {
  line-height: 41px;
}
</style>
