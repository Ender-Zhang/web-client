<!--
 * @Author: Ender-Zhang YUZ302@pitt.edu
 * @Date: 2023-04-23 14:46:21
 * @LastEditors: Ender-Zhang YUZ302@pitt.edu
 * @LastEditTime: 2023-04-23 16:45:37
 * @FilePath: \web-client\src\pages\Admin\Children\AdminUpdate.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div class="goods-detail">
    <div class="goods-detail-top">Product Information</div>
    <div class="goods-detail-group">
      <div class="goods-icon">
        <span>Pictures</span>
        <img :src="goodsInfo.thumb_url" alt="">
      </div>
      <div class="goods-item">
        <span>ID</span>
        <span>{{ goodsInfo.goods_id }}</span>
      </div>
      <div class="goods-item">
        <span>title</span>
        <el-input
          type="text"
          placeholder="Please enter content"
          v-model="goodsInfo.short_name"
          clearable
          style="width:350px"
        >
        </el-input>
      </div>
      <div class="goods-item">
        <span>Classification</span>
        <el-select v-model="goodsInfo.category" placeholder="Please select">
          <el-option
           v-for="item in options"
          :key="item.value"
          :label="item.label"
          :value="item.value">
          </el-option>
        </el-select>
      </div>
      <div class="goods-item">
        <span>Price</span>
        <el-input
          type="number"
          placeholder="Please enter the price"
          v-model="goodsInfo.price"
          clearable
          style="width:100px"
        >
        </el-input>
      </div>
      <div class="goods-item">
        <span>Inventory</span>
        <el-input
          type="number"
          placeholder="Please enter inventory"
          v-model="goodsInfo.counts"
          clearable
          style="width:100px"
        >
        </el-input>
      </div>
      <div class="goods-item">
        <span>Description</span>
        <el-input
          type="text"
          placeholder="Please enter content"
          v-model="goodsInfo.goods_name"
          clearable
          style="width:450px"
        >
        </el-input>
      </div>
      <button @click="saveGoodsInfo()">Edit product information</button>
    </div>
  </div>
</template>

<script>
  import {mapState} from 'vuex';
  import {changeGoodsInfo,getAllgoods} from './../../../api/index';

  export default {
    data() {
      return {
        goodsInfo: {},
        options: [{
          value: 1,
          label: 'Beauty Whitening'
        }, {
          value: 2,
          label: 'Health Care'
        },{
          value: 3,
          label: 'Medical Books'
        },{
          value: 4,
          label: 'Medical Devices'
        },{
          value: 5,
          label: 'Vision Protection'
        }],
      }
    },
     mounted(){
        this.goodsInfo = JSON.parse(window.localStorage.getItem('goodsInfo'));
    },
    methods:{
      // 修改商品信息
      async saveGoodsInfo(){
        this.goodsInfo.price = Number(this.goodsInfo.price);
        this.goodsInfo.counts = Number(this.goodsInfo.counts);
        let result = await changeGoodsInfo(this.goodsInfo);
        if(result.success_code === 200){
          this.$message({
              type: 'success',
              message: 'Modified successfully'
            });
          this.$router.replace('/admin');
          getAllgoods();
        }
      }
    },
  }
</script>

<style scoped lang="stylus" ref="stylesheet/stylus">
  .goods-detail
    width 70%
    height 100%
    margin 20px auto
    .goods-detail-top
      width 100%
      height 60px
      line-height 60px
      padding-left 10px
      font-weight bold
    .goods-detail-group
      .goods-icon
        height 60px
        padding 0 10px
        background-color #fff
        border-bottom: 1px solid #e0e0e0
        display flex
        justify-content space-between
        align-items center
        img
          width 50px
          border-radius 50%
      .goods-item
        height 50px
        padding 0 10px
        background-color #fff
        border-bottom: 1px solid #e0e0e0
        display flex
        justify-content space-between
        align-items center
        input
          border 1px solid #ccc
          outline none
          text-align right
          width 200px
      button
        width 20%
        height 40px
        line-height 40px
        background-color #e9232c
        text-align center
        margin 60px 0
        border none
        font-size 16px
        color #fff
        border-radius 10px
    .right-title-color
      color #999
      font-size 14px
</style>

