<template>
  <div class="bgf">
    <div id="body">
      <i-input right title="拼团活动名称" placeholder="请填写拼团名称" maxlength="20" @change="name" v-model="namePingTuan" />
      <div class="border1px"></div>
      <!-- <div class="changeTime" @click="startTime">
      <span>拼团开始时间</span>
      <span>{{currentDate1}}</span>
      </div>-->
      <div class="changeTime">
        <!-- <span>拼团结束时间</span> -->
        <span @click="startTime">{{currentDate1}}</span>
        <span @click="endTime">{{currentDate2}}</span>
      </div>
      <div class="border1px"></div>
      <div class="chooseNum">
        <p>拼团人数</p>
        <div class="box" v-for="(val,idx) in array" :key="idx">
          <div>
            <input
              maxlength="4"
              :value="inputVal1[idx]"
              type="number"
              placeholder="0"
              @input="getInputVal1"
            />
          </div>
          <div>人</div>
          <div>
            <input
              type="digit"
              maxlength="6"
              :value="inputVal2[idx]"
              placeholder="0"
              @input="getInputVal2"
            />
          </div>
          <div>¥</div>
          <div @click="removed">
            <img class="dele" src="../../../static/img/delete.png" alt />
          </div>
        </div>
        <div class="addBox" @click="addBox">+添加价格</div>
      </div>
      <div class="clear"></div>
      <div class="pingtuanTop">
        <span>拼团页面顶部图</span>
      </div>
      <div class="border1px"></div>
      <div class="border1px"></div>
      <div class="imgJia">
        <img class="addImg" @click="topimage" src="../../../static/img/jh_img@2x.png" />
        <div class="asdf" v-for="(top,index) in topimgList" :key="index" @click="delrImgTop(index)">
          <img class="imgtop" :src="top" />
          <span class="imhSpa">-</span>
        </div>
      </div>

      <!--<input id="photo" type="file" accept="image/*">-->
      <div class="clear"></div>
      <div class="fuwuDetail">
        <p>服务详情</p>
        <textarea placeholder="请输入服务详情" v-model="info"></textarea>
        <div class="fuwuImg">
          <img src="../../../static/img/jh_img@2x.png" class="addImg" @click="fuwuimage" />
          <!-- <img class="imgfuwu" :src="fuwu" v-for="(fuwu,index) in fuwuimgList" :key="index" /> -->
          <div
            class="asdf"
            v-for="(fuwu,index) in fuwuimgList"
            :key="index"
            @click="delrImgfuw(index)"
          >
            <img class="imgtop" :src="fuwu" />
            <span class="imhSpa">-</span>
          </div>
        </div>
      </div>
      <div class="clear"></div>
      <i-input
        right
        title="美容院名称"
        placeholder="请美人院名称"
        @change="shopnm"
        v-model="shopname"
        maxlength="11"
      />
      <div class="border1px"></div>
      <i-input right title="地址位置" placeholder="请输入详细地址" @change="addr" maxlength="20" v-model="address" />
      <div class="border1px"></div>
      <i-input right title="客服电话" type="number" placeholder="请输入客服电话" @change="tel" maxlength="20" v-model="telNalue" />
      <div class="border1px"></div>
      <!-- <i-input right title="客服电话" placeholder="请输入客服电话" @change="tel" maxlength="20" />
      <div class="border1px"></div>-->
      <div class="fuwuDetail">
        <p>参团须知</p>
        <textarea v-model="cantuanxuzhi"></textarea>
      </div>
      <van-popup :show="showStartTime" position="bottom" overlay="false" @close="onStartClose">
        <van-datetime-picker
          type="datetime"
          :min-date="minDate1"
          :max-date="maxDate"
          @cancel="inStartClose"
          @input="onInputStart"
          @confirm="closeTimeChange"
        />
      </van-popup>
      <van-popup :show="showEndTime" position="bottom" overlay="false" @close="onEndClose">
        <van-datetime-picker
          type="datetime"
          :min-date="minDate1"
          :max-date="maxDate"
          @cancel="inEndClose"
          @input="onInputEnd"
          @confirm="closeTimeChange2"
        />
      </van-popup>
    </div>
    <span class="last" @click="publish">发布活动</span>
  </div>
</template>
<script>
var time = require("../../utils/index.js");
export default {
  data() {
    return {
      cantuanxuzhi: "",
      namePingTuan: "",
      num: '',
      address: "",
      telNalue: "",
      minDate1: new Date().getTime(),
      maxDate: new Date(2019, 10, 1).getTime(),
      currentDate1: "拼团开始时间", //用户选择的起始时间
      showStartTime: false, //是否显示开始时间选择框
      currentDate2: "拼团结束时间", //用户选择的结束时间
      showEndTime: false, //是否显示结束时间选择框,
      imgtop: "",
      imgfuwu: "",
      shopname: "",
      info: "",
      topimgList: [],
      fuwuimgList: [],
      array: [1], //默认显示一个
      inputVal1: [""], //所有input的内容
      inputVal2: [""],
      numJiSuan1:1,
      numJiSuan2:1,
    };
  },
  methods: {
    name(e) {
      this.namePingTuan = e.target.detail.value;
    },
    addr(e) {
      this.address = e.target.detail.value;
    },
    number(e) {
      this.num = e.target.detail.value;
    },
    shopnm(e) {
      this.shopname = e.target.detail.value;
    },
    tel(e) {
      this.telNalue = e.target.detail.value;
    },
    topimage() {
      var that = this;
      wx.chooseImage({
        sizeType: ["compressed"], // 可以指定是原图还是压缩图，默认二者都有
        sourceType: ["album", "camera"], // 可以指定来源是相册还是相机，默认二者都有
        success: function(res) {
          for (var i = 0; i < res.tempFiles.length; i++) {
            if (res.tempFiles[i].size > 2000000) {
              wx.showToast({
                title: "图片大小超出上限",
                icon: "none",
                duration: 1000
              });
              return;
            }
          }
          // that.data.evalList.push.apply(that.data.evalList, res.tempFilePaths); //数组合并
          // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
          var tempFilePaths = res.tempFilePaths; //that.data.evalList//
          //启动上传等待中...  上传提示框
          wx.showToast({
            title: "正在上传...",
            icon: "loading",
            mask: true,
            duration: 10000
          });
          // var uploadImgCount = 0;
          //遍历每个图片 去执行uploadFile
          for (var i = 0, h = tempFilePaths.length; i < h; i++) {
            if (i >= 4) {
              wx.showToast({
                title: "图片数量超出上限",
                icon: "none",
                duration: 1000
              });
              return;
            }
            wx.uploadFile({
              url:
                "https://www.meifuyihao.com/index.php/routine/Store/uploadimg", //接受文件的url
              filePath: tempFilePaths[i], //字符串
              name: "file", //后台接受的字段名称
              success: function(res) {
                var data = JSON.parse(res.data);
                //that.imgList.push(data.replace(/[\\]/g, '').replace(/\"/g, ""))  //得到的数据添加到imgList中
                that.topimgList.push(data.data.url);
                //如果是最后一张,则隐藏等待中
                // if (uploadImgCount == tempFilePaths.length) {
                wx.hideToast();
                // }
              },
              fail: function(res) {
                wx.hideToast();
                wx.showModal({
                  title: "错误提示",
                  content: "上传图片失败",
                  showCancel: false,
                  success: function(res) {}
                });
              }
            });
          }
        }
      });
    },
    fuwuimage() {
      var that = this;
      wx.chooseImage({
        sizeType: ["compressed"], // 可以指定是原图还是压缩图，默认二者都有
        sourceType: ["album", "camera"], // 可以指定来源是相册还是相机，默认二者都有
        success: function(res) {
          for (var i = 0; i < res.tempFiles.length; i++) {
            if (res.tempFiles[i].size > 2000000) {
              wx.showToast({
                title: "图片大小超出上限",
                icon: "none",
                duration: 1000
              });
              return;
            }
          }
          // that.data.evalList.push.apply(that.data.evalList, res.tempFilePaths); //数组合并
          // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
          var tempFilePaths = res.tempFilePaths; //that.data.evalList//
          //启动上传等待中...  上传提示框
          wx.showToast({
            title: "正在上传...",
            icon: "loading",
            mask: true,
            duration: 10000
          });
          //遍历每个图片 去执行uploadFile
          for (var i = 0, h = tempFilePaths.length; i < h; i++) {
            if (i >= 7) {
              wx.showToast({
                title: "图片数量超出上限",
                icon: "none",
                duration: 1000
              });
              return;
            }
            wx.uploadFile({
              url:
                "https://www.meifuyihao.com/index.php/routine/Store/uploadimg", //接受文件的url
              filePath: tempFilePaths[i], //字符串
              name: "file", //后台接受的字段名称
              /*header: {
              'token': wx.getStorageSync('token')
            },*/
              success: function(res) {
                var data = JSON.parse(res.data);
                //that.imgList.push(data.replace(/[\\]/g, '').replace(/\"/g, ""))  //得到的数据添加到imgList中
                that.fuwuimgList.push(data.data.url);
                //如果是最后一张,则隐藏等待中
                // if (uploadImgCount == tempFilePaths.length) {
                wx.hideToast();
                // }
              },
              fail: function(res) {
                wx.hideToast();
                wx.showModal({
                  title: "错误提示",
                  content: "上传图片失败",
                  showCancel: false,
                  success: function(res) {}
                });
              }
            });
          }
        }
      });
    },
    delrImgTop(id) {
      this.topimgList.splice(id, 1);
    },
    delrImgfuw(id) {
      this.fuwuimgList.splice(id, 1);
    },
    publish() {
      var that = this;
      if (
        this.namePingTuan == "" ||
        this.address == "" ||
        this.shopname == "" ||
        this.cantuanxuzhi == "" ||
        this.info == ""
      ) {
        wx.showToast({
          title: "请全部填写",
          icon: "none",
          duration: 1000
        });
        return;
      }
      if (
        this.telNalue == ""
      ) {
        wx.showToast({
          title: "请全部填写",
          icon: "none",
          duration: 1000
        });
        return;
      }
      if (!/^[0-9]+$/.test(this.telNalue)) {
        wx.showToast({
          title: "电话格式不符",
          icon: "none",
          duration: 1000
        });
        return false;
      }
      var obj = {
        sid: wx.getStorageSync("sid"),
        pname: this.namePingTuan,
        address: that.address,
        add_time: this.currentDate1,
        stop_time: this.currentDate2,
        detail_image: that.fuwuimgList.join(),
        picture: that.topimgList.join(),
        shop_name: that.shopname,
        notice: this.cantuanxuzhi,
        info: this.info,
        service_tel: this.telNalue,
        directions: "",
        num:'',
        price_2:'',
        price_3:'',
        people_2:'',
        people_3:'',

      };
      for (var i = 0; i < this.inputVal1.length; i++) {
        if (this.inputVal1[i] == "") {
          wx.showToast({
            title: "人数不能为0!",
            icon: "none",
            duration: 1000
          });
          return;
        }
        obj[`people_${i + 1}`] = this.inputVal1[i];
      }
      for (var i = 0; i < this.inputVal2.length; i++) {
        if (this.inputVal2[i] == "") {
          wx.showToast({
            title: "价格不能为0!",
            icon: "none",
            duration: 1000
          });
          return;
        }
        obj[`price_${i + 1}`] = this.inputVal2[i];
      }

      this.$axios.post("routine/Store/addPink", obj).then(function(response) {
        wx.showToast({
          title: response.data.msg,
          icon: "none",
          duration: 1000
        });
        if(response.data.code==400){
          return
        }
        wx.navigateTo({
          url: "/pages/pingTuan/main"
        });
      });
    },
    //点击取消关闭开始时间选择框
    inStartClose() {
      this.showStartTime = false;
    },
    //点击取消关闭结束时间选择框
    inEndClose() {
      this.showEndTime = false;
    },
    // 关闭时间选择框
    closeTimeChange() {
      this.showStartTime = false;
    },
    closeTimeChange2() {
      this.showEndTime = false;
    },
    // 更新用户开始时间选择
    onInputStart(event) {
      if(this.numJiSuan1==1){
        this.numJiSuan1+=1
        return
      }
      this.currentDate1 = time.formatTime(event.mp.detail);
    },
    //更新用户结束时间选择
    onInputEnd(event) {
      if(this.numJiSuan2==1){
        this.numJiSuan2+=1
        return
      }
      this.currentDate2 = time.formatTime(event.mp.detail);
    },
    //点击遮罩层关闭开始时间选择框
    onStartClose(e) {
      this.showStartTime = false;
    },
    //点击遮罩层关闭结束时间选择框
    onEndClose() {
      this.showEndTime = false;
    },
    // 开启开始时间选择框
    startTime() {
      this.showStartTime = true;
    },
    // 开启结束时间选择框
    endTime() {
      this.showEndTime = true;
    },
    addBox() {
      if (this.array.length < 3) {
        this.array.push(1);
      } else {
        wx.showToast({
          title: "最多三人团",
          icon: "none",
          duration: 1000
        });
      }
    },
    removed(e) {
      var nowidx = e.currentTarget.dataset.eventid.split("_")[1];
      this.array.splice(nowidx, 1);
      this.inputVal1.splice(nowidx, 1);
      this.inputVal2.splice(nowidx, 1);
      if (this.array.length < 1) {
        oldarr = [0];
      }
    },
    getInputVal1(e) {
      var nowIdx = e.currentTarget.dataset.eventid.split("_")[1];
      var val = e.mp.detail.value;
      this.inputVal1[nowIdx] = val;
    },
    getInputVal2(e) {
      var nowIdx = e.currentTarget.dataset.eventid.split("_")[1];
      var val = e.mp.detail.value;
      this.inputVal2[nowIdx] = val;
    }
  }
};
</script>

<style scoped>
.changeTime {
  width: 375px;
  height: 49px;
  color: #333333;
  font-size: 15px;
}
.changeTime span:nth-child(1) {
  margin-top: 14px;
  margin-left: 15px;
  float: left;
}
.changeTime span:nth-child(2) {
  margin-top: 15px;
  margin-right: 15px;
  float: right;
}
.chooseNum {
  width: 375px;
  position: relative;
}
.chooseNum p {
  color: #333333;
  font-size: 15px;
  margin-top: 14px;
  margin-left: 15px;
  margin-bottom: 17px;
}
.box {
  width: 226px;
  height: 27px;
  border-radius: 7px;
  border: 1px solid #999999;
  display: flex;
  margin-left: 15px;
  margin-bottom: 15px;
}
.box div {
  width: 20%;
  height: 100%;
  text-align: center;
  line-height: 27px;
}
.box div:nth-child(2n) {
  background-color: #f35379;
  color: #ffffff;
  font-size: 12px;
}
.box div input {
  text-align: center;
  width: 100%;
  margin-top: 3px;
}
.addBox {
  font-size: 12px;
  color: #333333;
  border: 1px solid #333333;
  border-radius: 3px;
  padding: 3px;
  position: absolute;
  top: 41px;
  right: 50px;
}
.pingtuanTop {
  width: 375px;
  height: 49px;
}
.pingtuanTop span {
  margin-left: 15px;
  color: #333333;
  font-size: 15px;
  line-height: 49px;
}
.imgJia {
  width: 375px;
}
.addImg {
  width: 28px !important;
  height: 28px !important;
  margin-left: 15px !important;
  margin-top: 15px !important;
  margin-bottom: 15px !important;
  vertical-align: top !important;
  border: 1px solid #cccccc;
  padding: 15px;
}
.fuwuImg {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}
.fuwuImg > img {
  width: 28px;
  height: 28px;
  margin: 5px;
  border: 1px solid #cccccc;
  padding: 15px;
}
.asdf img {
  width: 100%;
  height: 100%;
}
.asdf {
  width: 58px;
  height: 58px;
  display: inline-block;
  position: relative;
  margin-left: 15px;
  margin-top: 15px;
  margin-bottom: 15px;
}
.imhSpa {
  width: 20px;
  height: 20px;
  position: absolute;
  right: -7px;
  top: -5px;
  background-color: #cccccc;
  border-radius: 10px;
  font-size: 30px;
  text-align: center;
  line-height: 16px;
}
.fuwuDetail p {
  color: #333333;
  font-size: 15px;
  margin-left: 15px;
  margin-top: 14px;
  margin-bottom: 20px;
}
.fuwuDetail textarea {
  width: 324px;
  height: 87px;
  padding: 6px 10px;
  font-size: 15px;
  border: 1px solid #cccccc;
  margin: 0 auto;
}

.last {
  display: block;
  width: 329px;
  height: 42px;
  background-color: #f35379;
  border-radius: 66px;
  margin: 39px auto;
  margin-bottom: 30px;
  color: #fefefe;
  text-align: center;
  line-height: 42px;
}
.dele {
  width: 17px;
  height: 17px;
  margin-top: 5px;
}
</style>
