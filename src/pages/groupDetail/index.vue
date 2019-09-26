<template>
  <div class="bgf">
    <div id="body">
      <!-- 1.0 轮播图 -->
      <swiper
        :indicator-dots="true"
        :autoplay="true"
        :interval="3000"
        :duration="1000"
        indicator-active-color="#ffffff"
        circular
      >
        <block v-for="(item,index) in swipers" :key="index">
          <swiper-item>
            <image :src="item" />
          </swiper-item>
        </block>
      </swiper>
      <!-- 购物街分享 -->
      <div class="fenx">
        <span>拼团</span>
        <img src="../../../static/img/fx_icon@2x.png" @click="handleOpen1" alt />
      </div>
      <div class="shopping">
        <div>
          <span>{{people}}人团</span>
          <p>{{pname}}</p>
          <p>¥{{price}}</p>
        </div>
      </div>
      <div class="clear"></div>
      <div class="serabble">
        <div class="serabble_play">
          <span>拼团玩法</span>
          <button @click="goToMyself">个人中心</button>
        </div>
        <div class="borf"></div>
        <div class="serabble_list">
          <div>
            <img src="../../../static/img/kt_icon@2x.png" alt />
            <p>开团/参团</p>
          </div>

          <div>
            <img src="../../../static/img/fk_icon@2x.png" alt />
            <p>付款</p>
          </div>
          <div>
            <img src="../../../static/img/yq_icon@2x.png" alt />
            <p>邀请好友</p>
          </div>
          <div>
            <img src="../../../static/img/ct_icon@2x.png" alt />
            <p>成团</p>
          </div>
        </div>
      </div>
      <div class="clear"></div>
      <!-- <i-cell title="正在拼团的小伙伴" value="更多"></i-cell> -->
      <i-cell
        title="正在拼团的小伙伴"
        value="更多"
        is-link
        only-tap-footer
        :url="'/pages/groupList1/main?pid='+pinkid"
      ></i-cell>
      <div v-for="(pink,index) in pinks" :key="index">
        <div class="serabblePeople" v-if="pink.users.length!=0">
          <div class="serabblePeople_Left">
            <img :src="pink.users[0].headimgurl" alt />
            <span>{{pink.users[0].nickname}}</span>
            <i>{{pink.remain}}人团</i>
          </div>
          <!-- <div v-if="pink.pink==1" class="serabblePeople_Right">已成团</div> -->
          <div class="serabblePeople_Right" @click="goToCan">去参团</div>
        </div>
      </div>

      <div class="clear"></div>
      <div class="datail">
        <p>服务详情</p>
        <div class="border1px"></div>
        <span>{{serviceinfo}}</span>
        <div class="dv"></div>
        <img
          :style="{height:comNum[idx]+'px !important' }"
          :src="val"
          v-for="(val,idx) in detail_image"
          :key="idx"
        />
      </div>
      <div class="clear"></div>
      <div class="datail">
        <p>参团须知</p>
        <div class="border1px"></div>
        <span>{{notice}}</span>
      </div>
      <div class="clear"></div>
      <div class="datail">
        <p>商家信息</p>
        <div class="border1px"></div>
        <p>名称: {{shop_name}}</p>
        <p>地址: {{address}}</p>
        <p>电话: {{tel}}</p>
      </div>
      <div v-if="sta==1" class="yiJieShu">活动未开始</div>
      <div v-else-if="sta==3" class="yiJieShu">活动已结束</div>
      <div v-else class="flex">
        <div
          v-for="(itm,ids) in tuan"
          class="footFix"
          @click="goToPay(ids,itm.pri)"
          :key="ids"
        >¥{{itm.pri}}&nbsp;&nbsp;({{itm.peo}}人团)</div>
      </div>
      <i-action-sheet
        :visible="visible1"
        :actions="actions1"
        show-cancel
        @cancel="handleCancel1"
        @click="handleClickItem"
      />
      <van-popup :show="isShows" z-index="9999999">
        <div style="padding:20px;" class="tanChu">
          <p>申请获取以下权限</p>
          <p>获得你的公开信息(昵称，头像等)</p>
          <button
            class="bottom"
            type="primary"
            lang="zh_CN"
            size="mini"
            open-type="getUserInfo"
            @getuserinfo="bindGetUserInfo"
          >授权登录</button>
        </div>
      </van-popup>
      <div class="addXiao" v-show="addXiao">
        <span></span>
        点击 [添加小程序] , 下次访问更便捷 >
      </div>
    </div>
    <div class="foot">
      <img src="../../../static/img/组 1256@2x.png" alt />
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      swipers: [],
      visible1: false,
      actions1: [
        {
          name: "转发给好友",
          openType: "share"
        },
        {
          name: "添加到我的小程序"
        }
      ],
      addXiao: false,
      code: NaN,
      serviceinfo: "",
      pinks: [],
      productid: 0,
      storeid: 0,
      price: 0,
      pname: "",
      people: 0,
      current: 0,
      detail_image: [],
      comNum: [],
      notice: [],
      shop_name: NaN,
      address: NaN,
      tel: NaN,
      isShows: true,
      pageLis: [
        {
          name: "确定"
        }
      ],
      pinkid: NaN,
      tuan: [],
      dui: false,
      sta: NaN
    };
  },
  onLoad(options) {
    this.pinkid = options.scene;
    this.gonggong();
  },
  // onShow() {
  // this.gonggong();
  // },
  //转发
  onShareAppMessage: function(res) {
    var that = this;
    if (res.from === "button") {
    }
    return {
      title: "转发",
      path: "/pages/groupDetail/main?scene=" + that.pinkid,
      success: function(res) {}
    };
  },
  methods: {
    goToCan() {
      var that = this;
      wx.navigateTo({
        url: "/pages/groupDetail/main?scene=" + that.pinkid
      });
    },
    gonggong() {
      this.dui = false;
      wx.login({
        success: res => {
          if (res.code) {
            wx.request({
              url:
                "https://www.meifuyihao.com/index.php/routine/logins/setCode",
              method: "post",
              dataType: "json",
              data: { info: res },
              success: function(response) {
                wx.setStorageSync("openid", response.data.openid);
                wx.setStorageSync("session_key", response.data.session_key);
              }
            });
          } else {
          }
        }
      });
      var that = this;
      if (wx.getStorageSync("pdata")) {
        this.$axios
          .post("routine/logins/index", {
            info: wx.getStorageSync("pdata")
          })
          .then(function(response) {
            that.isShows = false;
            that.shuju();
          });
        that.isShows = false;
      }
    },
    imgHeight(wid) {
      var thar = this;
      thar.comNum = [];
      for (var i = 0; i < wid.length; i++) {
        wx.getImageInfo({
          src: wid[i],
          success(res) {
            thar.comNum.push(375 / res.width * res.height);
          }
        });
      }
    },
    handleOpen1() {
      this.visible1 = true;
    },
    handleCancel1() {
      this.visible1 = false;
    },
    goToMyself() {
      wx.navigateTo({
        url: "/pages/PersonalCenter/main"
      });
    },
    goToPay(peo, pri) {
      var that = this;
      this.$axios
        .post("routine/users/is_participate", {
          openid: wx.getStorageSync("openid"),
          id: that.pinkid
        })
        .then(function(res) {
          if (res.data.data == true) {
            wx.showToast({
              title: "最多参加一个团！",
              icon: "none",
              duration: 2000
            });
            return;
          }

          if (this.dui) {
            wx.showToast({
              title: "该拼团已结束！",
              icon: "none",
              duration: 2000
            });
            return false;
          }
          wx.navigateTo({
            url:
              "/pages/pay/main?storeid=" +
              this.storeid +
              "&productid=" +
              this.productid +
              "&price=" +
              pri +
              "&pname=" +
              this.pname +
              "&people=" +
              (peo + 1)
          });
        });
    },
    handleClickItem(e) {
      this.visible1 = false;
      this.setAdd();
    },
    setAdd() {
      this.addXiao = true;
      setTimeout(() => {
        this.addXiao = false;
      }, 5000);
    },
    shuju() {
      this.imgheights = NaN;
      var that = this;
      var times = new Date().valueOf();
      this.$axios
        .post("routine/Users/combination_detail", {
          openid: wx.getStorageSync("openid"),
          id: that.pinkid
        })
        .then(function(response) {
          if (response.data.code == 400) {
            wx.showToast({
              title: "该拼团已结束！",
              icon: "none",
              duration: 2000
            });
            return;
          }
          that.swipers = [];
          that.tuan = [];
          that.serviceinfo = response.data.data.storeInfo.info;
          that.productid = response.data.data.storeInfo.id;
          that.storeid = response.data.data.storeInfo.uid;
          var cc = [];
          if (response.data.data.storeInfo.add_time * 1000 > times) {
            that.sta = 1;
          } else if (response.data.data.storeInfo.stop_time * 1000 < times) {
            that.sta = 3;
          } else {
            that.sta = 2;
          }
          for (var j = 1; j <= 3; j++) {
            if (
              !response.data.data.storeInfo["price_" + j] ||
              response.data.data.storeInfo["price_" + j] == 0
            ) {
              break;
            }
            cc.push(+response.data.data.storeInfo["price_" + j]);
          }
          that.price = Math.min.apply(null, cc);
          that.pname = response.data.data.storeInfo.pname;
          for (var i = 1; i <= 3; i++) {
            var obj = {};
            if (response.data.data.storeInfo["price_" + i] == that.price) {
              that.people = response.data.data.storeInfo["people_" + i];
            }
            obj.peo = response.data.data.storeInfo["people_" + i];
            obj.pri = response.data.data.storeInfo["price_" + i];
            if (response.data.data.storeInfo["people_" + i]) {
              that.tuan.push(obj);
            }
          }
          that.notice = response.data.data.storeInfo.notice;
          that.shop_name = response.data.data.storeInfo.shop_name;
          that.address = response.data.data.storeInfo.address;
          that.tel = response.data.data.storeInfo.service_tel;
          var detail_image = response.data.data.storeInfo.detail_image;
          that.detail_image = detail_image.split(",");
          var pictures = response.data.data.storeInfo.picture;
          that.swipers = pictures;
          that.imgHeight(that.detail_image);
          that.zhenPing();
        });
    },
    zhenPing() {
      var that = this;
      this.$axios
        .post("routine/Users/participate_user", {
          openid: wx.getStorageSync("openid"),
          id: that.pinkid,
          limit: 6
        })
        .then(function(res) {
          if (res.data.data.status == 1) {
            wx.showToast({
              title: "该拼团已结束！",
              icon: "none",
              duration: 2000
            });
            that.dui = true;
            return;
          }
          that.pinks = res.data.data;
        });
    },
    bindGetUserInfo(e) {
      var that = this;
      var pdata = e.mp.detail;
      pdata.userInfo = e.mp.detail.userInfo;
      pdata.iv = encodeURI(e.mp.detail.iv);
      pdata.encryptedData = e.mp.detail.encryptedData;
      pdata.session_key = wx.getStorageSync("session_key");
      wx.setStorageSync("pdata", pdata);
      this.$axios
        .post("routine/logins/index", {
          info: wx.getStorageSync("pdata")
        })
        .then(function(response) {
          that.isShows = false;
          that.shuju();
        });
    }
  }
};
</script>     
<style scoped>
#body {
  background-color: #ffffff;
}
.addXiao {
  color: #000000;
  background-color: #ffffff;
  font-size: 14px;
  padding: 5px 10px;
  border-radius: 10px;
  position: fixed;
  top: 15px;
  right: 20px;
}
.addXiao span {
  display: block;
  position: absolute;
  width: 15px;
  height: 15px;
  transform: rotate(45deg);
  top: -7px;
  right: 47px;
  background-color: #ffffff;
}
swiper {
  text-align: center !important;
  height: 186px;
}
swiper image {
  width: 100%;
  height: 100%;
}
.shopping > div {
  float: left;
  margin-left: 15px;
  padding: 15px 0 10px 0;
}
.shopping > div p:nth-child(2) {
  font-size: 15px;
  color: #333333;
  line-height: 22px;
  float: left;
}
.shopping > div p:nth-child(3) {
  font-size: 20px;
  color: #e80000;
  line-height: 24px;
}
.fenx img {
  width: 25px;
  height: 25px;
}
.fenx {
  height: 50px;
  padding: 0 21px;
  background-color: #f35379;
  color: #ffffff;
  font-size: 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.shopping {
  position: relative;
  overflow: hidden;
}
.shopping span {
  width: 35px;
  height: 15px;
  border: 1px solid #f35379;
  font-size: 8px;
  text-align: center;
  line-height: 15px;
  color: #f35379;
  margin-right: 5px;
  float: left;
  margin-bottom: 4px;
}
.serabble {
  width: 690rpx;
  height: 138px;
  padding: 12px 15px 15px 15px;
  position: relative;
}
.serabble_play {
  overflow: hidden;
}
.serabble_play span:nth-child(1) {
  font-size: 15px;
  color: #333333ff;
  float: left;
}
.serabble_play button {
  font-size: 12px;
  color: #ffffffff;
  line-height: 24px;
  text-align: center;
  padding: 0;
  text-align: center;
  width: 69px;
  height: 24px;
  border-radius: 30px;
  background-color: #f35379;
  float: right;
  z-index: 99999;
}
.serabble_list {
  padding: 22px 12px 15px 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.serabble_list div {
  z-index: 99;
}
.serabble_list img {
  width: 40px;
  height: 40px;
  display: block;
  margin: 0 auto;
  margin-bottom: 10px;
}
.borf {
  border-bottom: 3px solid #f35379;
  width: 262px;
  margin-top: -50px;
  position: absolute;
  top: 127px;
  left: 55px;
}
.shopping_play .i-cell-text {
  font-size: 15px;
}
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
}
.serabblePeople .serabblePeople_Left i {
  /* width: 35px; */
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
.serabblePeople .serabblePeople_Right {
  color: #ffffff;
  float: right;
  margin-right: 15px;
  width: 69px;
  height: 24px;
  background-color: #f35379;
  font-size: 12px;
  text-align: center;
  line-height: 24px;
  margin-top: 19px;
  border-radius: 24px;
}
.serabblePeople_Right2 {
  font-size: 12px;
  color: #ffffff;
  padding: 0px 17px;
  border-radius: 24px;
  background-color: #f35379;
  float: right;
  margin-right: 15px;
  line-height: 60px;
  height: 24px;
  line-height: 24px;
  margin-top: 17px;
}
.datail {
  padding: 0 15px 30px 15px;
}
.datail p {
  margin-top: 3px;
  line-height: 37px;
  font-size: 15px;
  color: #333333;
}
.datail span {
  font-size: 14px;
  color: #666666;
}
.datail img {
  width: 100% !important;
}
.flex {
  display: flex;
  flex-wrap: nowrap;
  width: 375px;
  height: 49px;
  justify-content: space-between;
  position: fixed;
  bottom: 0;
  background-image: url("../../../static/img/bgcc.png");
  background-size: 100%;
}
.footFix {
  color: #ffffff;
  height: 49px;
  text-align: center;
  line-height: 49px;
  font-size: 15px;
  flex-grow: 1;
}
.yiJieShu {
  background-color: #bababa;
  color: #ffffff;
  position: fixed;
  bottom: 0;
  width: 375px;
  height: 49px;
  line-height: 49px;
  text-align: center;
}
.foot img {
  display: block;
  margin: 20px auto;
  width: 117px;
  height: 22px;
}
.foot {
  height: 85px;
}

.tanChu p {
  font-size: 15px;
  line-height: 50px;
  min-width: 250px;
}
.tanChu p:nth-child(2) {
  color: #cccccc;
}
.bottom {
  border-radius: 80rpx;
  margin-top: 12px;
  margin-left: 63px;
  font-size: 35rpx;
}
</style>
