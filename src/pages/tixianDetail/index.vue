<template>
  <div id="body">
    <div v-for="order in recordsb" :key="order.id">
      <div class="box">
        <div class="boxLeft">
          <p>{{name}}</p>
          <p>订单号：{{order.ordernob}}</p>
          <p>{{order.add_time}}</p>
        </div>
        <div class="boxRight">
          <p>+{{order.extract_price}}</p>
          <!-- <p>{{order.status==0?'审核中''提现成功'}}</p> -->
          <p v-if="order.status==0">审核中</p>
          <p v-else-if="order.status==-1">未通过</p>
          <p v-else>提现成功</p>
        </div>
      </div>
      <div class="border1px"></div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      records: [],
      recordsb: [],
      name:NaN
    };
  },
  onLoad(options) {
    var that = this;
    that.name=NaN
    that.name=options.name
    that.recordsb=[]
    this.$axios
      .post("routine/Store/getExtract", { sid: wx.getStorageSync("sid") })
      .then(function(response) {
        

        that.records = response.data.data;
        for (var i = 0; i < that.records.length; i++) {
          that.records[i].add_time = that.formatTime(
            that.records[i].add_time,
            "Y.M.D h:m:s"
          );
          if (options.tixian == 0) {
            if (that.records[i].status == 0) {
              that.recordsb.push(that.records[i]);
            }
          } else if (options.tixian == 2) {
            if (that.records[i].status == 1) {
              that.recordsb.push(that.records[i]);
              console.log(response);
              
            }
          } else {
            that.recordsb = that.records;
          }
        }
        console.log(that.recordsb);
      });
  },
  methods: {
    formatNumber(n) {
      n = n.toString();
      return n[1] ? n : "0" + n;
    },
    formatTime(number, format) {
      var formateArr = ["Y", "M", "D", "h", "m", "s"];
      var returnArr = [];
      var date = new Date(number * 1000);
      returnArr.push(date.getFullYear());
      returnArr.push(this.formatNumber(date.getMonth() + 1));
      returnArr.push(this.formatNumber(date.getDate()));
      returnArr.push(this.formatNumber(date.getHours()));
      returnArr.push(this.formatNumber(date.getMinutes()));
      returnArr.push(this.formatNumber(date.getSeconds()));
      for (var i in returnArr) {
        format = format.replace(formateArr[i], returnArr[i]);
      }
      return format;
    }
  }
};
</script>
<style scoped>
.box {
  height: 82px;
  width: 750rpx;
}
.boxLeft {
  margin-left: 15px;
  margin-top: 9px;
  float: left;
}
.boxLeft p:nth-child(1) {
  color: #333333;
  font-size: 15px;
}
.boxLeft p:nth-child(2) {
  color: #565664;
  font-size: 12px;
  margin-top: 5px;
}
.boxLeft p:nth-child(3) {
  color: #565664;
  font-size: 12px;
  margin-top: 5px;
}
.boxRight {
  margin-right: 15px;
  float: right;
  margin-top: 14px;
}
.boxRight p:nth-child(1) {
  color: #e80000;
  font-size: 20px;
}
.boxRight p:nth-child(2) {
  color: #565664;
  font-size: 12px;
  margin-top: 6px;
  text-align: right;
}
</style>
