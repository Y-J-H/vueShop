<template lang="html">
    <div class="" :data-id="productId">
      <nav-bread>
        <span>商品详情页</span> 
      </nav-bread>
      <div class="product-detail">
        <div class="product-img">
          <img v-lazy="'static/' + goods.productImage" alt="">
        </div>
        <div class="product-infor">
          <h3>{{ goods.productName }}</h3>
          <span class="product-price">&yen;{{ goods.salePrice }}</span>
          <button class="product-addCart" type="button" name="button" @click="addCart">加入购物车</button>
        </div>
      </div>

      <modal v-bind:mdShow="mdShow" v-on:close="closeModal">
        <p slot="message">
          请先登录
        </p>
        <div slot="btnGroup">
          <a class="btn btn--m" href="javascript:;" @click="mdShow = false">关闭</a>
          </div>
      </modal>

      <modal v-bind:mdShow="mdShowCart" v-on:close="closeModal">
        <p slot="message">
          <svg class="icon-status-ok">
            <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#icon-status-ok"></use>
          </svg>
          <span>加入购物车成!</span>
        </p>
        <div slot="btnGroup">
          <a class="btn btn--m" href="javascript:;" @click="mdShowCart = false">继续购物</a>
          <router-link class="btn btn--m btn--red" href="javascript:;" to="/cart">查看购物车</router-link>
        </div>
      </modal>
    </div>
</template>

<script>
import Public from '../Public'
import Modal from '@/components/Modal'
export default {
  mixins: [Public],
  components: {
    Modal
  },
  data () {
    return {
      productId: '',    // 产品id
      mdShow: false,
      mdShowCart: false,
      goods: {          // 商品详情

      }
    }
  },
  mounted () {
    this.productId = this.$route.params.productId
    this.getGoods()
  },
  methods: {
    closeModal () {
      this.mdShowCart = false
      this.mdShow = false
    },
    getGoods () {    // 获取商品详情数据
      this.$http.get(`/goods/detail?productId=${this.productId}`)      // 根据id获取商品详情
      .then(res => {
        res = res.data
        if (res.status === '0') {
          this.goods = res.result
        }
      })
    },
    addCart () {    // 加入购物车
      this.$http.post('/goods/addCart', {productId: this.productId})
      .then(res => {
        res = res.data
        if (res.status === '0') {
          this.mdShowCart = true
          this.$store.commit('updateCartCount', 1)
        } else {
          this.mdShow = true
        }
      })
    }
  }
}
</script>
