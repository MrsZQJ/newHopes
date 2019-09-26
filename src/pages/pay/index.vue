<template>
  <div id="body">
    <div class="Commodity_Information">
      <img src="../../../static/img/jihui.png" alt="">
      <div class="CommodityText">
        <p>{{pname}}</p>
        <p></p>
        <p>¥{{price}}</p>
      </div>
    </div>
    <div class="border1px"></div>
    <i-input
      v-model="realName"
      right
      title="姓名"
      autofocus
      placeholder="请填写您的姓名"
      maxlength="11"
      @change="name"
    />
    <div class="border1px"></div>
    <i-input
      v-model="phone"
      right
      title="手机号码"
      autofocus
      placeholder="请填写手机号码"
      maxlength="11"
      @change="tel"
    />
    <div class="border1px"></div>
    <!-- <i-cell title="微信支付">
      <i-checkbox slot="footer"></i-checkbox>
    </i-cell>-->
    <i-checkbox position="right" value="微信支付" color="#51c332" checked @click="handleChange"></i-checkbox>
    <div class="border1px"></div>
    <div class="pay_footer">
      <div class="footerLeft">
        <p>
          <span><span style="color:#333333;">付款</span>¥{{price}}</span>
        </p>
      </div>
      <div class="footerRight" @click="paySuccess" :style="{backgroundColor:changColor +'!important'}">立即支付</div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      realName: "",
      phone: undefined,
      weixinPay: false,
      cartId: 0,
      sid: 0,
      price: 0,
      pname: "",
      changColor:'#0086f8',
      people:NaN
    };
  },
  onLoad(options) {
    this.price = options.price;
    this.sid = options.storeid;
    this.pname = options.pname;
    this.people = options.people;
    var that = this;
    this.$axios
      .post("routine/Users/now_buy", {
        openid: wx.getStorageSync("openid"),
        cartNum: 1,
        productId: options.productid,
        price: that.price,
        people:that.people
      })
      .then(function(response) {
        if (response.data.code == 400) {
          wx.showToast({
            title: "该拼团已结束",
            icon: "none",
            duration: 1000
          });
          that.changColor='#cccccc'
          return;
        }
        that.changColor='#0086f8'        
        that.cartId = response.data.data.cartId;
      });
  },

  methods: {
    handleChange() {
      // this.weixinPay=!this.weixinPay
    },
    name(e) {
      this.realName = e.target.detail.value;
    },
    tel(e) {
      this.phone = e.target.detail.value;
    },
    paySuccess() {      
      if(this.changColor=='#cccccc'){
        return false
      }
      var that = this;
      if (this.realName == "" || this.phone == undefined) {
        wx.showToast({
          title: "请全部输入",
          icon: "none",
          duration: 1000
        });
        return;
      }
      wx.request({
        url: "https://www.meifuyihao.com/index.php/routine/Users/create_order",
        method: "POST",
        data: {
          openid: wx.getStorageSync("openid"),
          payType: "weixin",
          realName: that.realName,
          phone: that.phone,
          pinkId: 0,
          sid: that.sid,
          cartId: that.cartId
        },
        success: function(res) {
          var data = res.data.data;
          if (res.data.code == 200 && res.data.data.status == "SUCCESS") {
            wx.showToast({
              title: res.data.msg,
              icon: "success",
              duration: 1000
            });
            setTimeout(function() {
              wx.navigateTo({
                url: "/pages/paySuccess/main"
              });
            }, 1200);
          } else if (
            res.data.code == 200 &&
            res.data.data.status == "ORDER_EXIST"
          ) {
            wx.showToast({
              title: res.data.msg,
              icon: "none",
              duration: 1000
            });
            setTimeout(function() {
              wx.navigateTo({
                url: "/pages/groupDetail/main"
              });
            }, 1200);
          } else if (
            res.data.code == 200 &&
            res.data.data.status == "EXTEND_ORDER"
          ) {
            wx.showToast({
              title: res.data.msg,
              icon: "none",
              duration: 1000
            });
            setTimeout(function() {
              wx.navigateTo({
                url: "/pages/groupDetail/main"
              });
            }, 1200);
          } else if (
            res.data.code == 200 &&
            res.data.data.status == "WECHAT_PAY"
          ) {
            var jsConfig = res.data.data.result.jsConfig;
            wx.requestPayment({
              timeStamp: jsConfig.timestamp,
              nonceStr: jsConfig.nonceStr,
              package: jsConfig.package,
              signType: jsConfig.signType,
              paySign: jsConfig.paySign,
              success: function(res) {
                wx.showToast({
                  title: "支付成功",
                  icon: "success",
                  duration: 1000
                });
                setTimeout(function() {
                  wx.navigateTo({
                    url: "/pages/paySuccess/main"
                  });
                }, 1200);
              },
              fail: function(res) {
                wx.showToast({
                  title: "支付取消",
                  icon: "none",
                  duration: 1000
                });
                setTimeout(function() {
                  wx.navigateTo({
                    url: "/pages/groupDetail/main"
                  });
                }, 1200);
              },
              complete: function(res) {
                if (res.errMsg == "requestPayment:cancel") {
                  wx.showToast({
                    title: "取消支付",
                    icon: "none",
                    duration: 1000
                  });
                  setTimeout(function() {
                    wx.navigateTo({
                      //跳转至指定页面并关闭其他打开的所有页面（这个最好用在返回至首页的的时候）
                      url: "/pages/groupDetail/main"
                    });
                  }, 1200);
                }
              }
            });
          } else {
            wx.showToast({
              title: res.data.msg,
              icon: "none",
              duration: 1000
            });
          }
        }
      });
    }
  }
};
</script>

<style scoped>
.Commodity_Information {
  /* height: 70px; */
  display: flex;
  padding-left: 25px;
}
.Commodity_Information img {
  width: 80px;
  height: 80px;
  /* margin-left: 15px; */
  margin-top: 10px;
  margin-right: 10px;
}
.CommodityText {
  margin-top: 14px;
  margin-bottom: 11px;
}
.CommodityText p:nth-child(1) {
  font-size: 15px;
  color: #333333;
}
.CommodityText p:nth-child(2) {
  color: #999999;
  font-size: 12px;
  margin-bottom: 10px;
}
.CommodityText p:nth-child(3) {
  color: #e80000;
  font-size: 20px;
}
.pay_footer {
  position: fixed;
  bottom: 0;
  width: 750rpx;
  height: 49px;
}
.footerLeft {
  width: 255px;
  font-size: 15px;
  line-height: 49px;
  overflow: hidden;
  float: left;
}
.footerLeft p {
  line-height: 49px;
  margin-left: 15px;
  color: #333333;
}
.footerLeft p span {
  color: #e80000;
}
.footerRight {
  background-color: #f35379;
  color: #ffffff;
  width: 120px;
  height: 49px;
  text-align: center;
  line-height: 49px;
  float: left;
}
</style>
