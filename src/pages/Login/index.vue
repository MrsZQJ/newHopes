<template>
  <div id="body">
    <div :style="{'margin-top':topHeight}" class="login">
      <img src="../../../static/img/logo@2x.png" alt />
      <div class="mei">美达达</div>
      <div class="shu">
        <input type="number" placeholder="手机号码登录" v-model="phone" />
        <input type="password" placeholder="请输入密码" v-model="password" />
        <button @click="goToHome">登录</button>
        <span @click="goToPassWord">忘记密码？</span>
        <br />

        <i-toast id="toast" />
      </div>
      <button @click="call" class="lianxi">
        <img src="../../../static/img/lx_icon@2x.png" alt />
        <span>联系我们</span>
      </button>
    </div>
  </div>
</template>
<script>
const { $Toast } = require("../../../static/iview/base/index");
export default {
  data() {
    return {
      phone: "13632509970",
      password: "12345"
    };
  },
  created() {},
  onLoad() {
    wx.login({
      success: res => {
        if (res.code) {
          wx.request({
            url: "https://www.meifuyihao.com/index.php/routine/logins/setCode",
            method: "post",
            dataType: "json",
            data: { info: res },
            success: function(response) {
              wx.setStorageSync("openid", response.data.openid);
              wx.setStorageSync("session_key", response.data.session_key);
            }
          });
        } else {
          console.log(res.errMsg);
        }
      }
    });
  },
  computed: {
    topHeight() {
      var aaa = "";
      wx.getSystemInfo({
        success: res => {
          aaa = res.statusBarHeight + "px";
        }
      });
      return aaa;
    }
  },
  methods: {
    goToHome() {
      var that = this;
      if (this.phone == "") {
        $Toast({
          content: "手机号码不能为空!",
          icon: "prompt",
          mask: false
        });
        return false;
      }
      if (this.password == "") {
        $Toast({
          content: "密码不能为空!",
          icon: "prompt",
          mask: false
        });
        return false;
      }
      if (!/^1[3456789]\d{9}$/.test(this.phone)) {
        $Toast({
          content: "手机号码格式不对!",
          icon: "prompt",
          mask: false
        });
        return false;
      }
      that.$axios
        .post("routine/Logins/login", {
          user_name: that.phone,
          user_passwd: that.password
        })
        .then(function(response) {
          wx.setStorageSync("sid", response.data.data.id);
          if (response.data.code == "200") {
            // wx.switchTab({
            //   url: "/pages/home/main"
            // });
            wx.navigateTo({
              url: "/pages/Write-off/main"
            });
          } else {
            wx.showToast({
              title: response.data.msg,
              icon: "none",
              duration: 1000
            });
          }
        });
    },
    goToPassWord() {
      wx.navigateTo({
        url: "/pages/bangPhone/main"
      });
    },
    goUser() {
      wx.navigateTo({
        url: "/pages/authorization/main"
      });
    },
    call() {
      wx.makePhoneCall({
        phoneNumber: "13040887911"
      });
    }
  }
};
</script>
<style scoped>
.login {
  text-align: center;
}
.login > img {
  width: 75px;
  height: 75px;
  margin-top: 104px;
  margin-bottom: 7px;
}
.mei {
  font-size: 22px;
  color: #333333;
  margin-bottom: 64px;
}
.login .shu input,
.login .shu button {
  width: 271px;
  height: 45px;
  border-radius: 22px;
  text-align: center;
  border: 1px solid #d3d3d3;
  display: inline-block;
  margin-bottom: 15px;
  color: #a4a4a4;
  font-size: 10px;
}
.login .shu button {
  font-size: 18px;
  background-color: #f35379;
  line-height: 45px;
  color: #ffffff;
  margin-top: 8px;
  margin-bottom: 0px;
  border: none !important;
}
.shu span {
  color: #a1a8b0;
  font-size: 10px;
  margin-right: 70px;
  margin-top: 5px;
  float: right;
}
.lianxi {
  color: #ffffff;
  position: fixed;
  bottom: 67px;
  left: 268px;
  border-top-left-radius: 36px;
  border-bottom-left-radius: 36px;
  background-color: #f35379;
  width: 107px;
  height: 36px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.lianxi span {
  font-size: 15px;
}
.lianxi img {
  width: 19px;
  height: 19px;
  margin-left: -5px;
}
</style>
