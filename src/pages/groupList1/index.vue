<template>
  <div id="body">
    <div v-for="(pink,index) in pinks" :key="index">
      <div class="serabblePeople" v-if="pink.users.length!=0">
        <div class="serabblePeople_Left">
          <img :src="pink.users[0].headimgurl" alt />
          <span>{{pink.users[0].nickname}}</span>
          <i>{{pink.people}}人团</i>
          <p>还差{{pink.remain}}人拼成</p>
        </div>
        <div class="serabblePeople_Right">{{pink.status==0?'未成团':'已成团'}}</div>
      </div>
      <div class="border1px"></div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      pinks: [],
      num:0
    };
  },
  onLoad(options) {
    var that = this;
    that.$axios.post("routine/Store/participate_user", {
      id: options.pid,
      limit: 0
    }).then(function(res){
      that.pinks = res.data.data;
    })
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
.serabblePeople .serabblePeople_Left p{
  position: absolute;
  color: #999999;
  font-size: 14px;
  left: 67%;
  top: 50%;
  transform: translateX(-50%) translateY(-50%);
}
.serabblePeople .serabblePeople_Right {
  font-size: 14px;
  color: #ea6584;
  float: right;
  margin-right: 15px;
  line-height: 60px;
}
</style>
