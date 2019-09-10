<template>
  <div id="body">
    <div v-for="(order,index) in records" :key="index">
      <i-cell :title="order.shop_name" :label="order.stop_time">
        <div slot="footer" class="updata">
          <p>+{{order.price}}</p>
          <p>交易成功</p>
        </div>
      </i-cell>
      <div class="border1px"></div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      records: []
    };
  },
  onLoad() {
    var that = this;
    this.$axios
      .post("routine/Store/buy_list", { sid: wx.getStorageSync("sid") })
      .then(function(response) {
        that.records = response.data.data;
        for (var i = 0; i < that.records.length; i++) {
          that.records[i].stop_time=that. formatTime(that.records[i].stop_time,'Y.M.D');
        }
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
.updata {
  margin-right: 15px;
}
.updata p:nth-child(1) {
  font-size: 20px;
  color: #e80000;
}
.updata p:nth-child(2) {
  color: #c7c7cc;
  font-size: 12px;
}
</style>
