<template>
  <div>
      <van-nav-bar
        title="我的订单"
        left-text="返回"
        left-arrow
        @click-left="$router.back()"
      />
      <van-tabs v-model="active" color="#1989fa">
        <van-tab title="全部"></van-tab>
        <van-tab title="待支付"></van-tab>
        <van-tab title="已支付"></van-tab>
        <van-tab title="退款单"></van-tab>
      </van-tabs>
      <loading v-if="loadingShow" />
      <div v-if="!loadingShow">
          <van-card
          v-for="(item,index) in orderResult"
          :key="index"
          :num="item.person.length"
          :price="item.allprice + '.00'"
          :desc="'出发时间:' + item.depDate + ',' + ' 共' + item.person.length + '人'"
          :title="item.dep +  '→' + item.arr"
          thumb="https://img.yzcdn.cn/vant/ipad.jpeg"
        >
          <template #tags>
            <van-tag plain type="danger" v-if="item.baoxian.indexOf('9') != -1">已购货运险</van-tag>
            <van-tag plain type="danger" v-if="item.baoxian.indexOf('28') != -1">已购机身险</van-tag>
          </template>
          <template #footer>
            <van-button size="mini" type="danger" @click="deleteCartItem(index)">删除</van-button>
            <van-button size="mini" type="primary">付款</van-button>
          </template>
        </van-card>
      </div>
  </div>
</template>

<script>
import loading from '@/components/common/loading.vue'
import { get_orderinfo,buy_stateOrder } from '@/api/order.js'
export default {
  data() {
    return {
      loadingShow:true,
      active:0,
      orderResult:[],
      falg:true
    }
  },
  components:{
    loading
  },
  methods: {
    bannerNum() {
     if(this.falg) {
        this.active = Number(this.$route.query.banner)
        this.falg = false
     }
    }
  },
  watch:{
    active:{
      handler() {
         this.loadingShow = true
          setTimeout(() => {
            this.loadingShow = false
          },500)
          if(this.$route.query.banner) {
            this.bannerNum(this.falg)
          }
          //查询所有订单
          if(this.active == 0) {
            get_orderinfo(localStorage.getItem('userId')).then(res => {
              this.orderResult = [...res.data.data]
            })
          }
          //buy_state 传 1 代表查询待支付订单,传 2 查询已支付订单  带上userId
          if(this.active == 1 || this.active == 2) {
            buy_stateOrder({
              buy_state:this.active,
              id:localStorage.getItem('userId')
            }).then(res => {
              this.orderResult = [...res.data.data]
            })
          }
          //获取退款单数据
          if(this.active == 3) {
            
          }

      },
      immediate:true  //进来获取全部订单
    },
  }
}
</script>

<style>

</style>