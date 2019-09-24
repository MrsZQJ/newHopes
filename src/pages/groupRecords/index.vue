<template>
  <div id="body">
    <div class="search">
      <div class="searchBor" @click="GoTohexiao">
        <i-icon type="search" size="20" />
        <input type="text" placeholder="请输入核销码" />
        <div>核销</div>
      </div>
    </div>
    <div v-for="(order,index) in records" :key="index">
      <div class="recordsList">
        <div class="recordsList_left">
          <img :src="order.avatar" alt />
          <div>
            <p>{{order.user_name}}</p>
            <p>{{order.phone}}</p>
            <p>
              <span>{{order.people}}人团</span> |
              <span>已参与{{order.count_people}}人</span>
            </p>
            <p>2019-09-09  12:20:37</p>
          </div>
        </div>
        <div class="recordsListRight">
          <div>
            <i>{{order.is_shop==0?'未到店':'已到店'}}</i>
            <!-- <i>{{order.k_id==0?'团长':'团员'}}</i> -->
          </div>
          <img
            @click="CallPhone(index)"
            src="https://www.meifuyihao.com/public/uploads/images/小程序美达达图标/电话/dh_icon@2x.png"
            alt
          />
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
      records: []
    };
  },
  onLoad() {
    var that = this;
    this.$axios
      .post("routine/Store/pink_order_list", { sid: wx.getStorageSync("sid") })
      .then(function(response) {
        that.records = response.data.data;
        console.log(response);
        
      });
  },
  methods: {
    GoTogroupDetail(id) {
      wx.navigateTo({
        url: "/pages/groupDetail1/main?pinkid=" + id
      });
    },
    GoTohexiao() {
      wx.navigateTo({
        url: "/pages/hexiao/main"
      });
    },
    CallPhone(i) {
      var thar = this;
      wx.makePhoneCall({
        phoneNumber: thar.records[i].phone
      });
    }
  }
};
</script>

<style scoped>
.search {
  width: 750rpx;
  height: 49px;
}
.searchBor {
  margin: 10px auto;
  width: 335px;
  height: 19px;
  padding: 6px 0;
}
.searchBor i-icon {
  margin-left: 15px;
  float: left;
}
.searchBor input {
  font-size: 13px;
  margin-left: 10px;
  color: #bebebe;
  float: left;
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
.recordsList {
  width: 375px;
  height: 80px;
  position: relative;
}
.recordsList_left {
  float: left;
  display: flex;
}
.recordsList_left img {
  width: 40px;
  height: 40px;
  border-radius: 40px;
  margin-left: 15px;
  margin-right: 10px;
  margin-top: 20px;
}
.recordsList_left div {
  margin-top: 7px;
  min-width: 150px;
}
.recordsList_left p:nth-child(1) {
  font-size: 15px;
  color: #333333;
}
.recordsList_left p:nth-child(2) {
  font-size: 13px;
  color: #666666;
}
.recordsList_left p:nth-child(3) {
  color: #999999;
  font-size: 10px;
}
.recordsList_left p:nth-child(4) {
  color: #999999;
  font-size: 10px;
}
.recordsListRight i {
  width: auto;
  height: 15px;
  border-radius: 20px;
  font-size: 8px;
  position: absolute;
  padding: 0 7px;
}
.recordsListRight i:nth-child(1) {
  border: 1px solid #999999;
  color: #999999;
  text-align: center;
  line-height: 15px;
  top: 10px;
  right: 15px;
}
.recordsListRight i:nth-child(2) {
  border: 1px solid #ea6584;
  color: #ea6584;
  text-align: center;
  line-height: 15px;
  top: 10px;
  right: 68px;
}
.recordsListRight {
  float: right;
  margin-right: 15px;
}
.recordsListRight img {
  width: 20px;
  height: 20px;
  margin-top: 40px;
}
</style>
