<template>
  <div class="bgf">
    <div id="body">
      <div class="search">
        <div class="searchBor" @click="GoTohexiao">
          <i-icon type="search" size="20" />
          <input type="text" placeholder="请输入核销码" />
          <div>核销</div>
        </div>
      </div>

      <div v-for="(pink,index) in pinks" :key="index">
        <div class="gouWuJie">
          <img src="../../../static/img/lb.png" alt />
          <p>{{pink.pname}}</p>
          <span v-if="pink.sta==2">进行中</span>
          <span v-else-if="pink.sta==1">未开始</span>
          <span v-else>已结束</span>
        </div>
        <div class="moneyDetail">
          <div class="moneyDetailLeft" @click="GoTogroupDetail(pink.id)">
            <p v-for="(obj,idx) in pink.arr" :key="idx">
              <span>团</span>
              <span>{{obj.people}}人团</span>
              <span>¥{{obj.price}}</span>
            </p>
          </div>
          <div class="moneyDetailRight">
            <div>
              <p>{{pink.count_people}}</p>
              <p>参团人数</p>
            </div>
            <div>
              <p>¥{{pink.count_price}}</p>
              <p>总收入</p>
            </div>
          </div>
          <div class="moneyDetailBot">
            <block v-if="pink.sta==2">
              <p @click="haibao(pink.id)">生成海报</p>
              <p @click="GoToGroupRecords">拼团记录</p>
              <!-- <p>立即成团</p> -->
            </block>
            <block v-else-if="pink.sta==1">
              <p @click="resetEdit(pink.id)">重新编辑</p>
              <p @click="haibao(pink.id)">生成海报</p>
              <p @click="GoToGroupRecords">拼团记录</p>
            </block>
            <block v-else>
              <p @click="haibao(pink.id)">生成海报</p>
              <p @click="GoToGroupRecords">拼团记录</p>
              <!-- <p class="like">立即成团</p> -->
            </block>
          </div>
        </div>
        <div class="clear"></div>
      </div>
      <img @click="NewPingTuan" src="../../../static/img/xj_icon@2x.png" class="NewJia" alt />
      <span @click="NewPingTuan" class="NewJian">新建拼团</span>
      <mp-dialog
        title="请选择模板"
        mask
        mask-closable
        :show="isShow"
        @buttontap="tapDialogButton"
        :buttons="buttons"
      >
        <view>
          <swiper :indicator-dots="indicatorDots" style="height:450px" @change="chang">
            <swiper-item>
              <canvas class="asd" canvas-id="myCanvas1"></canvas>
            </swiper-item>
            <swiper-item>
              <canvas class="asd" canvas-id="myCanvas2"></canvas>
            </swiper-item>
            <swiper-item>
              <canvas class="asd" canvas-id="myCanvas3"></canvas>
            </swiper-item>
            <swiper-item>
              <canvas class="asd" canvas-id="myCanvas4"></canvas>
            </swiper-item>
            <swiper-item>
              <canvas class="asd" canvas-id="myCanvas5"></canvas>
            </swiper-item>
          </swiper>
        </view>
      </mp-dialog>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      pinks: [],
      isShow: false,
      indicatorDots: true,
      productimg: "",
      erweima: "",
      pname: "",
      price: 0,
      people: 0,
      current: 0,
      buttons: [{ text: "取消" }, { text: "确定" }]
    };
  },
  onLoad() {
    var that = this;
    that.isShow = false;
    var times = (new Date()).valueOf();
    that.$axios
      .post("routine/Store/pink_list", { sid: wx.getStorageSync("sid") })
      .then(function(response) {
        that.pinks = response.data.data;
        for (var i = 0; i < that.pinks.length; i++) {
          // console.log(that.pinks[i].status);
          
          that.pinks[i].arr = [];
          for (var j = 1; j <= 3; j++) {
            var obj = {};
            if (!that.pinks[i]["people_" + j]) {
              break;
            }
            obj.people = that.pinks[i]["people_" + j];
            obj.price = that.pinks[i]["price_" + j];
            that.pinks[i].arr.push(obj);
          }
          // console.log(new Date(parseInt(that.pinks[i].add_time) * 1000).toLocaleString().substr(0,17),that.pinks[i].add_time);
          // console.log(new Date(parseInt(times)).toLocaleString().substr(0,17),times);
          
          if (that.pinks[i].add_time*1000 > times) {
            that.pinks[i].sta = 1;
          } else if (that.pinks[i].stop_time*1000 < times) {
            that.pinks[i].sta = 3;
          } else {
            that.pinks[i].sta = 2;
          }
        }
      });
  },
  methods: {
    resetEdit(pid){
      wx.navigateTo({
        url: '/pages/resetEdit/main?pid='+pid,
      })
    },
    tapDialogButton(e) {
      if (e.mp.detail.index == 0) {
        this.isShow = false;
      } else if (e.mp.detail.index == 1) {
        var as = this.current + 1;
        this.save(as);
        this.isShow = false;
      }
    },
    haibao(pid) {
      var that = this;
      this.$axios
        .post("routine/Merchant/pinkingCode", {
          id: pid,
          page: "pages/groupDetail/main"
        })
        .then(function(response) {
          that.pname = response.data.data.pname;
          var cc = [];
          for (var j = 1; j <= 3; j++) {
            if (
              !response.data.data["price_" + j]
            ) {
              break;
            }
            cc.push(+response.data.data["price_" + j]);
          }
          that.price = Math.min.apply(null, cc);
          for (var i = 1; i <= 3; i++) {
            var obj = {};
            if (response.data.data["price_" + i] == that.price) {
              that.people = response.data.data["people_" + i];
            }
          }
          wx.showLoading({
            title: "海报生成中...",
            mask: true
          });
          wx.getImageInfo({
            src: response.data.data.poster,
            success: function(res) {
              that.erweima = res.path;
              wx.getImageInfo({
                src: response.data.data.picture,
                success: function(res) {
                  that.productimg = res.path;
                  that.hebing("myCanvas1", "/static/images/11.jpg");
                  that.hebing("myCanvas2", "/static/images/22.jpg");
                  that.hebing("myCanvas3", "/static/images/33.jpg");
                  that.hebing("myCanvas4", "/static/images/44.jpg");
                  that.hebing("myCanvas5", "/static/images/55.jpg");
                  wx.hideLoading();
                  that.isShow = true;
                }
              });
            }
          });
        });
    },
    chang(e) {
      this.current = e.mp.detail.current;
    },
    hebing(cid, bg) {
      var abc = NaN;
      wx.getSystemInfo({
        success: res => {
          abc = res.windowWidth / 375;
        }
      });

      const ctx = wx.createCanvasContext(cid);
      ctx.scale(abc, abc);
      ctx.drawImage(bg, 0, 0, 300, 450);
      ctx.fillStyle = "#ffffff";
      ctx.fillRect(15, 100, 270, 320);
      ctx.drawImage(this.productimg, 30, 110, 240, 180);
      ctx.setFillStyle("#000000");
      ctx.setFontSize(14);
      ctx.fillText(this.pname, 30, 330);
      ctx.setFillStyle("#dc3e1b");
      ctx.setFontSize(16);
      ctx.fillText("￥" + this.price, 30, 360);
      ctx.setFillStyle("#369599");
      ctx.setFontSize(12);
      ctx.fillText(this.people + "人团", 110, 357);
      ctx.setFillStyle("#d6d1d7");
      ctx.setFontSize(10);
      ctx.fillText("长按识别小程序码查看商品", 30, 380);
      ctx.drawImage(this.erweima, 155, 290, 120, 120);
      ctx.draw();
    },
    NewPingTuan() {
      wx.navigateTo({
        url: "/pages/newPingTuan/main"
      });
    },
    GoToGroupRecords() {
      wx.navigateTo({
        url: "/pages/groupRecords/main"
      });
    },
    GoTohexiao() {
      wx.navigateTo({
        url: "/pages/hexiao/main"
      });
    },
    GoTogroupDetail(id) {
      wx.navigateTo({
        url: "/pages/groupDetail1/main?pinkid=" + id
      });
    },
    canvasIdErrorCallback(cid) {
      this.shengc(cid);
    },
    shengc(cid) {
      wx.canvasToTempFilePath(
        {
          canvasId: cid,
          success(res) {
            wx.authorize({
              scope: "scope.writePhotosAlbum",
              success() {
                wx.saveImageToPhotosAlbum({
                  filePath: res.tempFilePath,
                  success() {
                    wx.showToast({
                      title: "图片保存成功"
                    });
                  }
                });
              }
            });
          }
        },
        this
      );
    },
    save(cid) {
      this.shengc("myCanvas" + cid);
    }
  }
};
</script>

<style scoped>
.posd {
  position: fixed;
  bottom: 0;
  width: 100%;
}
.asd {
  margin: 0px auto;
  width: 300px;
  height: 450px;
}

.search {
  width: 750rpx;
  height: 49px;
}
.searchBor {
  margin: 10px auto;
  width: 335px;
  height: 19px;
  padding: 6px 0;
  /* border: 1px solid #cccccc; */
  border-radius: 31px;
  background-color: #f5f5f5;
}
.searchBor i-icon {
  margin-left: 15px;
  float: left;
  margin-top: -3px;
}
.searchBor input {
  font-size: 13px;
  margin-left: 10px;
  color: #bebebe;
  float: left;
  margin-top: -3px;
}
.like {
  background-color: #e4e4e4 !important;
}
.searchBor div {
  width: 47px;
  border-left: 1px solid #999999;
  font-size: 13px;
  text-align: center;
  float: right;
  color: #333333;
  line-height: 19px;
}
.gouWuJie {
  width: 750rpx;
  height: 49px;
  line-height: 49px;
  font-size: 15px;
  color: #333333;
  text-align: left !important;
}
.gouWuJie p {
  text-align: left !important;
  float: left;
}
.gouWuJie img {
  width: 20px;
  height: 20px;
  float: left;
  vertical-align: middle;
  margin-right: 5px;
  margin-left: 15px;
  margin-top: 13px;
}
.gouWuJie span:nth-child(3) {
  float: right;
  margin-right: 15px;
}
.moneyDetail {
  height: 120px;
  overflow: hidden;
}
.moneyDetailLeft {
  margin-left: 15px;
  float: left;
  display: flex;
  flex-wrap: wrap;
  align-items: flex-end;
  height: 70px;
  width: 45%;
}
.moneyDetailLeft p {
  height: 20px;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  font-size: 15px;
  min-width: 165px;
}
.moneyDetailLeft span:nth-child(1) {
  font-size: 10px;
  background-color: #bebebe;
  color: #ffffff;
  padding: 2px;
  text-align: center;
  line-height: 16px;
}
.moneyDetailLeft span:nth-child(2) {
  color: #333333;
  font-size: 15px;
}
.moneyDetailLeft span:nth-child(3) {
  color: #e80000;
  font-size: 15px;
  margin-left: 13px;
}
.moneyDetailRight {
  float: right;
  display: flex;
}
.moneyDetailRight div {
  width: 69px;
  margin-right: 20px;
  text-align: center;
}
.moneyDetailRight div p {
  margin-top: 14px;
  margin-bottom: 7px;
}
.moneyDetailRight div p:nth-child(1) {
  color: #e70000;
  font-size: 15px;
}
.moneyDetailBot {
  overflow: hidden;
  height: 35px;
  width: 100%;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
.moneyDetailBot p {
  width: 69px;
  height: 24px;
  border-radius: 30px;
  background-color: #f35379;
  color: #ffffff;
  font-size: 12px;
  text-align: center;
  line-height: 24px;
  margin-right: 20px;
}
.rightTwo {
  margin-right: 24px;
}
.rightTwo p:nth-child(3) {
  background-color: #f35379;
}
.NewJia {
  width: 60px;
  height: 60px;
  position: fixed;
  left: 50%;
  transform: translate(-50%);
  bottom: 51px;
  margin-bottom: 51px;
  margin: 0 auto;
  display: block;
}
.NewJian {
  color: #333333;
  font-size: 15px;
  text-align: center;
  margin-bottom: 20px;
  display: block;
  position: fixed;
  left: 50%;
  transform: translate(-50%);
  bottom: 20px;
}
</style>
