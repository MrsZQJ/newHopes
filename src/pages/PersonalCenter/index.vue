<template>
  <div class="bgf">
    <div id="body">
      <div class="touxiang">
        <img :src="avatar" alt />
        <span>{{nickname}}</span>
      </div>
      <div class="myding">
        <div>我的订单</div>
        <div></div>
      </div>
      <!-- <div class="fen">
        <div>
          <span @click="cur=0" :class="{active:cur==0}">已支付</span>
        </div>
      </div>-->
      <!-- <div class="border1px"></div> -->
      <div v-show="cur==0" v-for="(order,index) in pays" :key="index" class="asdfg">
        <div class="border1px"></div>
        <i-cell title="店家名称" v-model="order.shop_name" is-link only-tap-footer></i-cell><div class="border1px"></div>
        <i-cell title="活动名称" v-model="order.pname"></i-cell><div class="border1px"></div>
        <i-cell title="地址" v-model="order.address"></i-cell><div class="border1px"></div>
        <i-cell title="电话" v-model="order.service_tel"></i-cell><div class="border1px"></div>
        <i-cell title="核销码" v-model="order.code"></i-cell>
        <div class="clear"></div>
        <!-- <img :src="[ order.is_shop==0 ? 'bgci2' : 'bgci1' ]"/> -->
        <img v-if="order.is_shop==0" class="bgci2" src="../../../static/img/weishiyong.png" alt="">
        <img v-else class="bgci1" src="../../../static/img/yishiyong.png" alt="">
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      dianjiamingcheng: "美哒哒美人院",
      huodongmingcheng: "购物节",
      ip: "广州天河区",
      tel: "15989468862",
      hexiaoma: "353234",
      cur: 0,
      nopays: [],
      pays: [],
      avatar: "",
      nickname: "",
    };
  },
  onLoad() {
    var that = this;
    this.$axios
      .post("routine/Users/myInfo", {
        openid: wx.getStorageSync("openid")
      })
      .then(function(response) {
        that.nopays = response.data.data.no_pay_order;
        that.pays = response.data.data.pay_order;
        that.avatar = response.data.data.avatar;
        that.nickname = response.data.data.nickname;
      });
  }
};
</script>

<style scoped>
.touxiang {
  width: 750rpx;
  height: 88px;
  text-align: left;
  line-height: 88px;
}
.touxiang img {
  width: 50px;
  height: 50px;
  margin-top: 19px;
  border-radius: 50px;
  margin-left: 20px;
  vertical-align: top;
}
.myding {
  height: 50px;
  display: flex;
  border-top: 1px solid #f5f5f5;
  border-bottom: 1px solid #f5f5f5;
}
.myding div {
  width: 49%;
  color: #3e89e4;
  font-size: 15px;
  text-align: left;
  padding-left: 15px;
  line-height: 50px;
}
.touxiang span {
  font-size: 15px;
  margin-left: 15px;
}
.fen {
  height: 49px;
  display: flex;
}
.fen > div {
  width: 50%;
  text-align: center;
  line-height: 49px;
}
.fen > div:nth-child(1) span {
  font-size: 15px;
  /*color: #C7C7CC;*/
}
.fen > div:nth-child(2) span {
  font-size: 15px;
  /* color: #C7C7CC;*/
  height: 49px;
  display: inline-block;
  border-bottom: 3px solid #3e89e4;
}
.active {
  color: #3e89e4;
}
.bgci1 {
  /* background-image: url("../../../static/img/weishiyong.png");
  background-color: aquamarine;
  background-size: 100px 100px;
  background-repeat: no-repeat;
  background-position: center; */
  position: absolute;
  width: 100px;
  height: 100px;
  z-index: 999999;
  top: 70px;
  left: 150px;
}
.bgci2 {
  /* background-image: url("../../../static/img/yishiyong.png");
  background-color: aquamarine;
  background-size: 100px 100px;
  background-repeat: no-repeat;
  background-position: center; */
  position: absolute;
  width: 100px;
  height: 100px;
  z-index: 999999;
  top: 70px;
  left: 150px;
}
.asdfg{
  position: relative;
}
</style>
