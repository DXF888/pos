<template>
	<div class="pos">
		<div>
			<el-row>
				<el-col :span='7'>
					<el-tabs>
						<el-tab-pane label="点餐" name="first">
							<el-table :data="tableData" border style="width: 100%">
								<el-table-column align="center" prop="goodsName" label="商品名称"></el-table-column>
								<el-table-column align="center" prop="count" label="量" width="60"></el-table-column>
								<el-table-column align="center" prop="price" label="金额" width="70"></el-table-column>
								<el-table-column align="center" label="操作" width="100" fixed="right">
									<template scope="scope">
										<el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
										<el-button type="text" size="small"  @click="addOrderList(scope.row)">增加</el-button>
									</template>
								</el-table-column>
							</el-table>
						</el-tab-pane>
						<el-tab-pane label="挂单" name="second">

						</el-tab-pane>
						<el-tab-pane label="外卖" name="third">

						</el-tab-pane>
					</el-tabs>
					<div class="totalDiv">
						<small>数量：</small>{{totalCount}}
						<small>金额：</small> {{totalMoney}}
					</div>
					<div class="div-btn" align="center">
						<el-button type="warning">挂单</el-button>
						<el-button type="danger" @click="delAllGoods()">删除</el-button>
						<el-button type="success" @click="checkout()">结账</el-button>
					</div>
				</el-col>
				<!--商品展示-->
				<el-col :span="17">
					<div class="convenience-goods">
						<h2>常用商品</h2>
						<div class="convenience-goods-list">
							<ul>
								<li v-for="goods in oftenGoods" @click="addOrderList(goods)">
									<span>{{goods.goodsName}}</span>
									<span class="price">￥{{goods.price}}元</span>
								</li>
							</ul>
						</div>
					</div>

					<div class="goods-type">
						<el-tabs>
							<el-tab-pane label="汉堡">
								<ul class='cookList'>
									<li v-for="food in type0Goods" @click="addOrderList(food)">
										<span class="foodImg"><img :src="food.goodsImg" width="100%"></span>
										<span class="foodName">{{food.goodsName}}</span>
										<span class="foodPrice">￥{{food.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="小食">
								<ul class='cookList'>
									<li v-for="food in type1Goods" @click="addOrderList(food)">
										<span class="foodImg"><img :src="food.goodsImg" width="100%"></span>
										<span class="foodName">{{food.goodsName}}</span>
										<span class="foodPrice">￥{{food.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="饮料">
								<ul class='cookList'>
									<li v-for="food in type2Goods" @click="addOrderList(food)">
										<span class="foodImg"><img :src="food.goodsImg" width="100%"></span>
										<span class="foodName">{{food.goodsName}}</span>
										<span class="foodPrice">￥{{food.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="套餐">
								<ul class='cookList'>
									<li v-for="food in type3Goods" @click="addOrderList(food)">
										<span class="foodImg"><img :src="food.goodsImg" width="100%"></span>
										<span class="foodName">{{food.goodsName}}</span>
										<span class="foodPrice">￥{{food.price}}元</span>
									</li>
								</ul>
							</el-tab-pane>
						</el-tabs>
					</div>
				</el-col>
			</el-row>

		</div>
	</div>
</template>

<script>
	import axios from 'axios'
	export default {
		name: 'Pos',
		data() {
			return {
				totalCount:0,
				totalMoney:0,
				tableData: [],
				oftenGoods: [],
				type0Goods: [],
				type1Goods: [],
				type2Goods: [],
				type3Goods: []
			}
		},
		created:function(){
			axios.get('http://jspang.com/DemoApi/oftenGoods.php')
			.then(reponse=>{
//				console.log(reponse);
				this.oftenGoods=reponse.data
			})
			.catch(error=>{
				console.log("获取数据失败");
			})
			
			axios.get('http://jspang.com/DemoApi/typeGoods.php')
			.then(reponse=>{
//				console.log(reponse);
				this.type0Goods=reponse.data[0];
				this.type1Goods=reponse.data[1];
				this.type2Goods=reponse.data[2];
				this.type3Goods=reponse.data[3];
			})
			.catch(error=>{
				console.log("获取数据失败");
			})
		},
		methods:{
			//添加订单列表的方法
			addOrderList(goods){
				this.totalCount=0;
				this.totalMoney=0;
				let isHave=false;
				//判断是否这个商品已经存在于订单列表
				for(let i=0; i<this.tableData.length;i++){
					if(this.tableData[i].goodsId == goods.goodsId){
						isHave=true;//存在
					}
				}
				//根据判断的值编写逻辑代码,根据订单列表里是否有这个商品
				if(isHave){
					//改变列表中商品数量
					let arr=this.tableData.filter(o=>o.goodsId == goods.goodsId);
					console.log(arr);
					arr[0].count++;
				}else{
					let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1}
					console.log(newGoods);
					this.tableData.push(newGoods);
				}
				this.getAllMoney();
			},
			//删除单个商品
			delSingleGoods(goods){
		        this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
      		},
			 //删除所有商品
	        delAllGoods() {
	            this.tableData = [];
	            this.totalCount = 0;
	            this.totalMoney = 0;
	        },
	        //汇总数量和金额
			getAllMoney() {
				this.totalCount = 0;
				this.totalMoney = 0;
				if(this.tableData) {
					this.tableData.forEach((element) => {
						this.totalCount += element.count;
						this.totalMoney = this.totalMoney + (element.price * element.count);
					});
				}

			},
			checkout() {
				if(this.totalCount != 0) {
					this.tableData = [];
					this.totalCount = 0;
					this.totalMoney = 0;
					this.$message({
						message: '结账成功，感谢你又为店里出了一份力!',
						type: 'success'
					});

				} else {
					this.$message.error('不能空结。老板了解你急切的心情！');
				}

			}
			}
			}
</script>

<style scoped="scoped">
	.div-btn {
		margin-top: 10px;
	}
	
	.el-col-17 {
		/*border-left: 1px solid #BFCBD9;*/
	}
	
	.convenience-goods h2 {
		height: 20px;
		line-height: 20px;
		border-bottom: 1px solid #D3DCE6;
		background-color: #F9FAFC;
		padding: 10px;
	}
	
	.convenience-goods-list li {
		cursor: pointer;
		list-style: none;
		float: left;
		border: 1px solid #E5E9F2;
		padding: 10px;
		margin: 5px;
		background-color: #fff;
		border-radius: 8px;
	}
	
	.price {
		color: #1D8CE0;
	}
	
	.goods-type {
		clear: both;
	}
	
	.cookList li {
		list-style: none;
		width: 23%;
		border: 1px solid #E5E9F2;
		height: auot;
		overflow: hidden;
		background-color: #fff;
		padding: 2px;
		float: left;
		margin: 2px;
		cursor: pointer;
	}
	
	.cookList .foodImg img {
		/*width: 10%;*/
		/*height: 10%;*/
	}
	
	.cookList li span {
		display: block;
		float: left;
	}
	
	.foodImg {
		width: 40%;
	}
	
	.foodName {
		font-size: 18px;
		padding-left: 10px;
		color: brown;
	}
	
	.foodPrice {
		font-size: 16px;
		padding-left: 10px;
		padding-top: 10px;
	}
	.totalDiv{
		height: 50px;
		line-height: 50px;
		text-align: center;
		background: #FFFFFF;
	}
</style>