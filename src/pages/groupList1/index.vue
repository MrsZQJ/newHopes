<template>
  <div id="body">
    <div v-for="(pink,index) in pinks" :key="index">
      <div class="serabblePeople">
        <div class="serabblePeople_Left" @click="goToTuan(pink[0].pid)">
          <img src="../../../static/img/logo.jpg" alt />
          <span>{{pink[0].name}}</span>
          <i>{{pink[0].people}}人团</i>
          <p>还差{{pink[0].remain}}人拼成</p>
        </div>
        <div class="serabblePeople_Right">{{pink[0].status==0?'进行中':'已成团'}}</div>
      </div>
    </div>
    <div class="border1px"></div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      pinks: [],
      num: 0,
      duic:true,
    };
  },
  onLoad(options) {
    var that = this;
    this.duic=true
    that.pinks = [];
    var asdy = [];
    if(options.nus==1){
      this.duic=false
    }
    that.$axios
      .post("routine/Store/participate_user", {
        sid: wx.getStorageSync("sid"),
        limit: 0
      })
      .then(function(res) {
        asdy = res.data.data;
        for (var i = 0; i < asdy.length; i++) {
          var mu = that.chuli(asdy[i]);
          asdy[i] = mu;
        }
        that.pinks = asdy;
      });
  },
  methods: {
    goToTuan(pinkid) {
      if(this.duic){
        return
      }
      wx.navigateTo({
        url: "/pages/groupDetail/main?scene=" + pinkid
      });
    },
    chuli(arr) {
      var ccc = arr;
      for (var j = 0; j < ccc.length; j++) {
        for (var z = 0; z < ccc.length - j - 1; z++) {
          // if (ccc[z].people) {
          //   ccc[z].people.length = 4;
          // }
          if (ccc[z].people < ccc[z + 1].people) {
            var temp = ccc[z];
            ccc[z] = ccc[z + 1];
            ccc[z + 1] = temp;
          }
        }
      }
      return ccc;
    }
  }
};
</script>     
<style scoped>
.serabblePeople {
  width: 750rpx;
  height: 60px;
  position: relative;
}
.serabblePeople .serabblePeople_Left {
  float: left;
  margin-left: 15px;
  display: flex;
}
.serabblePeople .serabblePeople_Left img {
  width: 40px;
  height: 40px;
  margin-top: 10px;
  margin-right: 10px;
  display: block;
}
.serabblePeople .serabblePeople_Left span {
  color: #333333;
  font-size: 15px;
  line-height: 60px;
  width: 75px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.serabblePeople .serabblePeople_Left i {
  padding: 0px 4px;
  height: 15px;
  border: 1px solid #ea6584;
  font-size: 8px;
  text-align: center;
  line-height: 15px;
  color: #ea6584;
  margin-left: 10px;
  margin-top: 20px;
}
.serabblePeople .serabblePeople_Left p {
  position: absolute;
  color: #999999;
  font-size: 14px;
  left: 67%;
  top: 50%;
  transform: translateX(-50%) translateY(-50%);
}
.serabblePeople .serabblePeople_Right {
  font-size: 14px;
  color: #333333;
  float: right;
  margin-right: 15px;
  line-height: 60px;
}
</style>
