<template>
	<div class="ratings" ref="Ratings">
		<div class="ratings-content">
			<div class="overview">
				<div class="over-left">
					<h1 class="score">{{seller.score}}</h1>
					<div class="title">综合评分</div>
					<div class="rank">高于周边商家{{seller.rankRate}}%</div>
				</div>
				<div class="over-right">
					<div class="score-wrapper">
			            <span class="title">服务态度</span>
			            <v-star :size="36" :score="seller.serviceScore"></v-star>
			            <span class="score">{{seller.serviceScore}}</span>
			        </div>
			        <div class="score-wrapper">
			            <span class="title">商品评分</span>
			            <v-star :size="36" :score="seller.foodScore"></v-star>
			            <span class="score">{{seller.foodScore}}</span>
			        </div>
			        <div class="delivery-wrapper">
			            <span class="title">送达时间</span>
			            <span class="delivery">{{seller.deliveryTime}}分钟</span>
			        </div>
				</div>
			</div>
			<v-split></v-split>
			<v-rating @select-tyle="SelectType" @only-Content="OnlyContent" :selectTypes="selectType" :ratings="ratings" :onlyContent="onlyContent"></v-rating>
			<div class="rating-wrapper">
				<ul>
					<li class="rating-item" v-for="rating in ratings" v-show="needShow(rating.rateType,rating.text)">
						<div class="avatar">
							<img :src="rating.avatar" width="28" height="28">
						</div>
						<div class="content">
							<h1 class="name">{{rating.username}}</h1>
							<div class="star-wrapper">
								<v-star :size="24" :score="rating.score"></v-star>
								<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
							</div>
							<p class="text">{{rating.text}}</p>
							<div class="recommend" v-show="rating.recommend && rating.recommend.length">
								<i class="iconfont" :class="{'fa fa-thumbs-up':rating.rateType===0,'fa fa-thumbs-down':rating.rateType===1}"></i>
								<span class="item" v-for='item in rating.recommend'>{{item}}</span>
							</div>
							<div class="time">
								{{rating.rateTime | formatDate}}
							</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>
<script>
import axios from 'axios';
import star from '../star/star.vue';
import split from '../split/split';
import BScroll from 'better-scroll';
import {formatDate} from '../../common/js/data.js';
import ratingselect from '../tatingselect/tatingselect';
	const ALL = 2;
	export default {
		name:"Rating",
		props:{
			seller:{
				type:Object
			}
		},
		data(){
			return{
				ratings:[],
				selectType:ALL,
				onlyContent:true
			}
		},
		created(){
			axios({
				method:'get',
				url:'http://localhost:8080/static/data.json'
			}).then(function res(e){
				this.ratings = e.data.ratings;

			}.bind(this));
			 // this.$http.get('http://localhost:8080/static/data.json').then(function res(e){
			 // 	console.log(e);
	   		//      this.rating = e.body.ratings;
   		 	//   });
   		 	this.$nextTick(function(){
   		 		this.scroll = new BScroll(this.$refs.Ratings, {click: true});
   		 	});	 
		},
		methods:{
			needShow(type,text){
				if(this.onlyContent && !text){
					return false;
				}
				if(this.selectType === ALL){
					return true;
				}else{
					return type == this.selectType
				}
			},
			SelectType(type){
				this.selectType = type;
				/*this.$nextTick(() => {
		          this.meunScroll.refresh();
		        });*/
			},
			OnlyContent(con){
				this.onlyContent = con;
				/*this.$nextTick(() => {
		          this.meunScroll.refresh();
		        });*/
			},
		},
		filters:{ 
			formatDate(time){
				let date = new Date(time);
				return formatDate(date,'yyyy-MM-dd hh:mm');
			}
		},
		components:{
			'v-star':star,
			'v-split':split,
			'v-rating':ratingselect
		}
	}
</script>
<style scoped lang="less">
@import url('../../common/css/font-awesome.min.css');
	.ratings{
		position: absolute;
		top:174px;
		bottom: 0;
		left:0;
  		width:100%;
  		overflow:hidden;
  		.overview{
  			display: flex;
  			padding: 18px 0 18px 18px;
  			.over-left{
  				 flex:0 0 137px;
			     width:137px;
			     padding:6px 0px;
			     border-right:1px solid rgba(7, 17, 27, 0.1);
			     text-align:center;
			     @media only screen and (max-width: 320px){
			     	flex:0 0 110px;
			     	width: 110px;
			     }
			    .score{
      				margin-bottom:12px;
			        line-height:28px;
			        font-size:24px;
			        color:rgb(255, 153, 0);
      			}
      			.title{
      				margin-bottom:8px;
			        line-height:12px;
			        font-size:12px;
			        color:rgb(7, 17, 27);
      			}
      			.rank{
      				line-height:10px;
			       	font-size:12px;
			        color:rgb(147, 153, 159);
      			}
  			}
  			.over-right{
  				flex:1;
      			padding:6px 0 6px 24px;
      			@media only screen and (max-width: 320px){
			     	padding-left: 6px;
			     }
      			.score-wrapper{
      				line-height:18px;
			        margin-top:8px;
			        font-size:0;
			        .title{
			        	display: inline-block;
			        	vertical-align:top;
				        line-height:18px;
				        font-size:12px;
				        color:rgb(7, 17, 27);
			        }
			        .star{
			        	 display:inline-block;
         				 margin:0 12px;
         				 vertical-align: top;
			        }
			        .score{
			        	display:inline-block;
				        vertical-align:top;
				        line-height:18px;
				        font-size:12px;
				        color:rgb(266, 153, 0);
			        }
      			}
      			.delivery-wrapper{
      				font-size: 0;
      				margin-top:5px; 
      				.title{
      					display:inline-block;
				        vertical-align:top;
				        line-height:18px;
				        font-size:12px;
				        color:rgb(7, 17, 27);
      				}
      				.delivery{
      					display:inline-block;
				        margin-left:12px;
				        vertical-align:top;
				        line-height:18px;
				        font-size:12px;
				        color:rgb(147, 153, 159);
      				}
      			}
  			}
  		}
  		.rating-wrapper{
  			padding: 0 18px;
  			.rating-item{
  				display: flex;
  				padding:18px 0;
  				border-top: 1px solid rgba(1,17,27,0.1);
  				.avatar{
  					flex:0 0 28px;
			        width:28px;
			        margin-right:12px;
			        img{
			        	border-radius:50%;
			        }
  				}
  				.content{
  					position:relative;
        			flex:1;
        			.name{
        				margin-bottom:4px;
				        line-height:12px;
				        font-weight:700;
				        font-size:10px;
				        color:rgb(7, 17, 27);
        			}
        			.star-wrapper{
				        	margin-bottom:6px;
          					font-size:0;
          					.star{
          						display:inline-block;
					            margin:0 16px 0 0;
					            vertical-align:top;
          					}
          					.delivery{
          						display:inline-block;
					            vertical-align:top;
					            font-size:10px;
					            line-height:12px;
					            color:rgb(147, 153, 159);
          					}
				        }
				    .text{
				        	line-height: 18px;
				        	color:rgb(7, 17, 27);
					        font-size:12px;
					        margin-bottom:8px;
				        }
				    .recommend{
				        	line-height:16px;
          					font-size:0;
          					.iconfont, .item{
          						display:inline-block;
					            margin:0 8px 4px 0;
					            font-size:9px;
          					}
          					.iconfont{
          						color:rgb(0, 160, 220);
          					}
          					.item{
          						padding:0 6px;
					            border:1px solid rgba(7, 17, 27, 0.1);
					            border-radius:1px;
					            color:rgb(147, 153, 159);
					            background:#fffff;
          					}
				        }
				    .time{
				        	position:absolute;
					        top:0;
					        right:0;
					        line-height:12px;
					        font-size:10px;
					        color:rgb(147, 153, 159);
				        }
  				}
  			}
  		}
	}
</style>