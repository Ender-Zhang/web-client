<template>
  <div id="container" v-if="goodsDetail[0]">
			<div class="pro_detail">
				<div class="pro_img" >
					<div class="tb_booth">
            <img :src="goodsDetail[0].image_url" class="pro_middle_img"/>
					</div>
				</div>
				<div class="pro_meg">
					<div class="pro_meg_hd">
						<h1>
					 		{{goodsDetail[0].goods_name}}
						</h1>
					</div>
					<div class="pro_meg_price">
						<dl>
							<dt>Promotion Price</dt>
							<dd>
								<div class="promo_price">
									<span class="tm-price">{{goodsDetail[0].price  | moneyFormat}}</span>
									<b>Promotion</b>
								</div>
							</dd>
						</dl>
            <dl>
							<dt>Market Price</dt>
							<dd class="nor_price">{{goodsDetail[0].normal_price /1000 | moneyFormat }}</dd>
						</dl>
						<dl>
							<dt>Our Store Offer</dt>
							<dd>Shipping included</dd>
						</dl>
            <dl>
							<dt class="sale_tip">{{goodsDetail[0].sales_tip}}</dt>
						</dl>
            <dl>
              <dt>Service Commitment</dt>
              <dd>
                <span>Genuine product guarantee</span>
                <span>Extremely fast returns</span>
              </dd>
            </dl>
					</div>
					<div class="pro_meg_deliver">
						<dl>
							<dt>Shipping Fee</dt>
							<dd>Pennsylvania&nbsp;&nbsp;To&nbsp;&nbsp;Pennsylvania&nbsp;&nbsp;&nbsp;Express delivery:0.00</dd>
						</dl>
					</div>
					<div class="pro_meg_console">
						<!-- <dl class="tb-sku">
							<dt>Quantity</dt>
							<dd>
								<div class="item-amout">
									<el-input-number v-model="shopNum" :min="1" :max="goodsDetail[0].counts"></el-input-number>
								</div>
								<span>Inventory</span><em>{{goodsDetail[0].counts}}</em><span>Pieces</span>
							</dd>
						</dl> -->
						<!-- <div class="shopping_car">
							<el-button type="danger" @click.prevent="dealWithCellBtnClick(goodsDetail[0])">加入购物车</el-button>
						</div> -->
					</div>
				</div>
			</div>
      <div class="pro_comment">
        <h3>Product Reviews</h3>
        <div v-if="goodsDetail[0].comments_count">
          <div class="media" v-for="(comment, index) in goodsComment" :key="index">
            <div class="media-body">
              <h6 class="media-heading" v-if="comment.user_nickname">User:&nbsp;{{ comment.user_nickname }}</h6>
              <h6 class="media-heading" v-else>User:&nbsp;{{ comment.user_name | nameFormat }}</h6>
              <span>Comments:</span>&nbsp;{{comment.comment_detail}}
              <el-rate
                v-model="comment.comment_rating"
                disabled
                show-score
                text-color="#ff9900">
              </el-rate>
            </div>
          </div>
        </div>
        <div class="media" v-else>
          <div class="media-body">
            No comments for this product
          </div>
        </div>
      </div>
      <div class="pro_judge" v-if="userInfo.user_name">
        <h3>Rate this item</h3>
        <span>Rate this product</span>
        <el-rate
          v-model="rating"
          :colors="colors"
          show-score
          text-color="#ff9900">
        </el-rate>
        <el-input
          type="textarea"
          autosize
          placeholder="请输入内容"
          v-model="textarea">
        </el-input>
        <el-button type="primary" @click="post()">Release<i class="el-icon-edit el-icon--right"></i></el-button>
      </div>
      <div class="pro_judge" v-else>
        <h3>Rate this item</h3>
        <span class="judge_erro_tip">Login to post comments</span>
      </div>
	</div>
</template>

<script>
  import {postComment, addGoodsToCart} from './../../api/index';
  import { MessageBox } from 'element-ui';
  import {mapActions} from 'vuex'
  import {mapState} from 'vuex'

  export default {
    data(){
      return{
        textarea: '',
        rating: 0,
        colors: ['#99A9BF', '#F7BA2A', '#FF9900'],
        currentGoodsId: 0,
        shopNum: 1,
      }
    },
    computed: {
      ...mapState(['goodsDetail','userInfo','goodsComment'])
    },
    created(){
      this.currentGoodsId = Number(this.$route.params.id);
      this.$store.dispatch('reqGoodsDetail', {
          goodsNo: this.currentGoodsId
      });
      this.$store.dispatch('reqGoodsComment', {
          goodsId: this.currentGoodsId
      });
    },
    watch:{
      $route(){
        this.currentGoodsId = Number(this.$route.params.id);
        this.$store.dispatch('reqGoodsDetail', {
          goodsNo: this.currentGoodsId
        });
        this.$store.dispatch('reqGoodsComment', {
          goodsId: this.currentGoodsId
        });
        // 请求当前用户信息
        this.$store.dispatch('getUserInfo');
      }
    },
    methods:{
      async post(){
        if(!this.textarea){
           MessageBox({
              type: 'info',
              message: "Comments must not be empty",
			        showClose: true,
           });
           return;
        }
        let result =  await postComment(this.goodsDetail[0].goods_id, this.textarea, this.rating, this.userInfo.id);
        if(result.success_code === 200){
          MessageBox({
              type: 'success',
              message: "Publish successfully",
			        showClose: true,
          });
          this.textarea = '';
          this.$store.dispatch('reqGoodsComment', {
            goodsId: this.currentGoodsId
          });
        }else{
          MessageBox({
              type: 'info',
              message: "Posting Failure",
			        showClose: true,
          });
        }
      },
      // 监听商品点击
      async dealWithCellBtnClick(goods){
         // 1. 发送请求
        // user_id, goods_id, goods_name, thumb_url, price, buy_count, counts
        if(this.userInfo.user_name){
          let result = await addGoodsToCart(this.userInfo.id, goods.goods_id, goods.short_name, goods.thumb_url, goods.price, this.shopNum, goods.counts);
          if(result.success_code === 200){
             MessageBox({
              type: 'success',
              message: result.message,
			        showClose: true,
            });
            let user_id = this.userInfo.id;
            // 请求商品数据
            this.$store.dispatch('reqCartsGoods',{user_id});
            this.shopNum = 1;
          }
        }
      }
    },
  }
</script>

<style scoped>
#container>.pro_detail{
	width: 990px;
  position: relative;
  z-index: 100;
  margin: 20px auto;
}
#container>.pro_comment{
	width: 73%;
  position: relative;
  margin: 40px auto 0;
  padding: 20px;
  border: 1px solid #ccc;
  border-bottom: none;
}
#container>.pro_judge{
	width: 73%;
  position: relative;
  margin: 0 auto 60px;
  padding: 20px;
  border-top: none;
  border: 1px solid #ccc;
}
.pro_judge>span{
  font-size: 12px;
  color: #ccc;
}
.pro_judge>.el-rate{
  display: inline-block;
}
.pro_judge .el-textarea{
  width: 50%;
  display: block;
  margin: 20px 0;
}
.pro_comment>h3{
  font-weight: bold;
  margin-bottom: 20px;
}
.pro_comment .media{
  border-top: 1px dashed #ccc;
  padding: 10px 0;
}
.pro_comment .media .media-heading{
  margin-bottom: 10px;
  font-weight: bolder;
}
.pro_comment .media .media-body{
  font-size: 14px;
}
.pro_comment .media .media-body span{
  font-weight: bolder;
}
.pro_comment .el-rate{
  display: inline-block;
  margin-left: 20px;
}
.pro_judge>h3{
  font-weight: bold;
  margin-bottom: 20px;
}
.pro_judge .judge_erro_tip{
  font-size: 15px;
  font-weight: bolder;
  color: #000;
}
.pro_detail>.pro_img{
    float: left;
    position: relative;
    padding: 100px 0;
    width: 480px;
    border: 1px solid #ccc
}
.pro_img>.tb_booth{
	position: relative;
  z-index: 1;
}
.tb_booth>.pro_middle_img{
	 width: auto;
  height: auto;
  max-width: 100%;
  max-height: 100%;
}
.pro_detail>.pro_meg{
	margin: 0 0 0 520px;
  color: #666;
  padding: 0 0 3px;
}
.pro_meg>.pro_meg_hd{
	padding: 0 10px 12px;
    color: #000;
}
.pro_meg_hd>h1{
    font-size: 16px;
    font-weight: 700;
    color: #000;
}
.pro_meg>.pro_meg_price{
  padding: 5px 20px;
  height: 200px;
  background-color:rgba(247, 244, 244, 0.6);

  display: flex;
  flex-direction: column;
  justify-content: space-around;
}
.pro_meg_price dl{
  display: flex;
  align-items: center;

  margin-bottom: 0 !important;
	cursor: pointer;
}
.pro_meg_price dl dt{
  width: 70px;
	color: #999;
  font-size: 12px;
}
.pro_meg_price dl dd{
  margin-bottom: 0 !important;
  font-family: Arial;
}
.pro_meg_price dl dd div{
  display: flex;
  align-items: center;
}
.pro_meg_price dl:last-child dd{
  color: #FF0036;
  font-weight: bold;
	font-size: 12px;
}
.promo_price{
	line-height: 24px;
  vertical-align: middle;
  color: #FF0036;
  font-size: 18px;
  font-family: Arial;
  -webkit-font-smoothing: antialiased;
}
.promo_price b{
	display: inline-block;
	font-weight: normal;
}
.promo_price b:last-child{
	font-size: 12px;
	background: #f47a86;
	color: white;
	padding: 0 6px;
}
.promo_price>.tm-price{
	font-size: 20px;
	display: inline-block;
	margin-right: 12px;
	font-weight: bold;
}
.nor_price{
  text-decoration: line-through;
}
.sale_tip{
  color: #FF0036 !important;
  font-weight: bolder;
  width: 80px  !important;
}
.pro_meg_deliver{
	margin: 5px 20px -15px 0;
	padding: 5px;
}
.pro_meg_deliver dl{
	padding: 5px;
	font-size: 14px;
	color: black;
	cursor: pointer;
}
.pro_meg_deliver dl dt{
	color: #999;
    font-size: 14px;
    text-align: left;
    width: 69px;
    margin: 0 0 0 8px;
    float: left;
}
.pro_meg_deliver dl dd{
	font-size: 13px;
}
.pro_meg_console{
	margin: 5px 20px 5px 0;
	padding: 5px;
}
.tb-sku{
	padding: 5px;
	font-size: 14px;
	color: black;
	cursor: pointer;
}
.tb-sku dt{
	color: #999;
    font-size: 14px;
    text-align: left;
    width: 69px;
    margin: 0 0 0 8px;
    float: left;
}
.tb-sku dd{
	font-size: 13px;
}
.tb-sku dd div{
	display: inline-block;
	margin-right: 20px;
}
.item-amout{
	height: 25px;
}
.item-amout a{
	display: inline-block;
    height: 23px;
    width: 17px;
    border: 1px solid #e5e5e5;
    background: #f0f0f0;
    text-align: center;
    line-height: 23px;
    color: #444;
    cursor: pointer;
}
.item-amout a:hover{
	color: #f50;
    border-color: #f60;
}
.item-amout>.text_amount{
	width: 40px;
	height: 20px;
	text-align: center;
	display: inline-block;
}
.shopping_car{
	margin: 20px auto 0;

  display: flex;
  justify-content: center;
}
.shopping_car button{
  outline: none;
}
</style>
