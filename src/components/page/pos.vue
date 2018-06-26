<template>
	<div class="pos">
		<!-- 这样引入element -->
		<el-row>
			<!-- 有7栏 -->
			<el-col :span='7' class="pos-order" id="order-list">
			  <el-tabs>
				 <el-tab-pane label="点餐">
				 	<el-table :data="tableData" border style="width:100%">
				 		<el-table-column prop="goodsName" label="商品名称">
				 		</el-table-column>
				 		<el-table-column prop="count" label="商品数量">
				 		</el-table-column>
				 		<el-table-column prop="price" label="商品价格">
				 		</el-table-column>d
				 		<el-table-column  label="操作" fixed="right">
				 			<template scope="scope">
				 				<el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除
				 				</el-button>
				 				<el-button type="text" size="small" @click="addOrderList(scope.row)">增加
				 				</el-button>
				 			</template>
				 		</el-table-column>
				 	</el-table>

				 	<div class="totalDiv">
				    <small> 数量：</small>{{totalCount}}&nbsp;&nbsp;&nbsp;<small>金额：</small> {{totalMoney}}元
			       </div>

				 	<div class="div-btn">
				 		<el-button type="warning">挂单
				 		</el-button>
				 		<el-button type="danger" @click="delAllGoods()">删除
				 		</el-button>
				 		<el-button type="success" @click="checkout()">结账
				 		</el-button>
				 	</div>
				 </el-tab-pane>
				 <el-tab-pane label="挂单">
				 	挂单
				 </el-tab-pane>
				 <el-tab-pane label="外卖">
				 	外卖
				 </el-tab-pane>
			  </el-tabs>
			</el-col>
			
			<!-- product 布局 17栏-->
			 <el-col :span="17">
			 	<div class="often-goods" >
			 		<div class="tittle">常用商品</div>
			 		<div class="often-goods-list">
			 			<ul>
			 				<li v-for="goods in oftenGoods" @click=addOrderList(goods)>
			 					<span>{{goods.goodsName}}</span>
			 					<span class="o-price">￥{{goods.price}}</span>
			 				</li>
			 			</ul>
			 		</div>
			 	</div>
			
            <!-- prodeuct type -->
			<div class="goods-type">
				<el-tabs>
					<el-tab-pane label="汉堡">
						<ul class='cookList'>                            
                            <li v-for="goods in type0Goods" @click=addOrderList(goods)>
                                <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                <span class="foodName">{{goods.goodsName}}</span>
                                <span class="foodPrice">￥{{goods.price}}元</span>
                            </li>
                        </ul>
					</el-tab-pane>
					<el-tab-pane label="小食">
						<ul class='cookList'>                            
                            <li v-for="goods in type1Goods" @click=addOrderList(goods)>
                                <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                <span class="foodName">{{goods.goodsName}}</span>
                                <span class="foodPrice">￥{{goods.price}}元</span>
                            </li>
                        </ul>
					</el-tab-pane>
					<el-tab-pane label="饮料" >
						<ul class='cookList'>                            
                            <li v-for="goods in type2Goods" @click=addOrderList(goods)>
                                <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                <span class="foodName">{{goods.goodsName}}</span>
                                <span class="foodPrice">￥{{goods.price}}元</span>
                            </li>
                        </ul>
					</el-tab-pane>
					<el-tab-pane label="套餐" >
						<ul class='cookList'>                            
                            <li v-for="goods in type3Goods" @click=addOrderList(goods)>
                                <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                <span class="foodName">{{goods.goodsName}}</span>
                                <span class="foodPrice">￥{{goods.price}}元</span>
                            </li>
                        </ul>
					</el-tab-pane>
				</el-tabs>
			</div>
			</el-col>
		</el-row>
	</div>
	
</template>

<script type="text/javascript">
	import axios from 'axios';
	export default{
		name:'pos',
		// data里面声明一个变量，变量里面是数组
		data(){
			return{
				// 实际项目中的数据都是从后台取的
				tableData:[],
				oftenGoods:[],
                type0Goods:[],
                type1Goods:[],
                type2Goods:[],
                type3Goods:[],
                totalMoney:0,
                totalCount:0
			}
		},
		created:function(){
			// oftenGoods 商品读取
			axios.get('http://jspang.com/DemoApi/oftenGoods.php')
			.then(reponse=>{
				// console.log(reponse);
				this.oftenGoods=reponse.data;

			}).catch(error=>{
				console.log(error);
				alert('网络错误，不能访问');
			}),
			// typeGoods 类型商品读取
			axios.get('http://jspang.com/DemoApi/typeGoods.php')
			.then(reponse=>{
				// console.log(reponse);
				this.type0Goods=reponse.data[0];
				this.type1Goods=reponse.data[1];
				this.type2Goods=reponse.data[2];
				this.type3Goods=reponse.data[3];
			}).catch(error=>{
				console.log(error);
				alert('网络错误，不能访问');
			})
		
		},
		// 生命周期
		mounted:function(){ 
			var orderHeight=document.body.clientHeight;
            // console.login(orderHeight);
            document.getElementById('order-list').style.height=orderHeight+'px';
		},
		methods:{
			// 添加商品
			addOrderList(goods){
				// 判断商品是否在商品例表中
                 let isHave=false;
                 for(let i=0;i<this.tableData.length;i++){
                 if(this.tableData[i].goodsId==goods.goodsId){
                 	isHave=true;
                   }
                 }
				// 根据判断的值编写业务逻辑
				if(isHave){
					// 改变列表中商品的数量
					let arr=this.tableData.filter(o=>o.goodsId == goods.goodsId);	
					arr[0].count++;			
			}else{
				let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1}
				this.tableData.push(newGoods);

			} 
			this.getAllMoney();   

		},
		// 删除单个订单(用数组的过滤方法)
		delSingleGoods(goods){
			console.log(goods);
			this.tableData=this.tableData.filter(o=>o.goodsId != goods.goodsId)
            this.getAllMoney();
		},
		// 计算总体数量和金额
		getAllMoney(){
			this.totalMoney=0;
			this.totalCount=0;
			// 判断tableData有值的社会
			if(this.tableData){
			this.tableData.forEach((element) =>{
				this.totalCount+=element.count;
				this.totalMoney=this.totalMoney+(element.price*element.count)
			});
		 }
		},
		// 删除整个订单
		 delAllGoods(){
		 	this.tableData=[];
		 	this.totalMoney=0;
		 	this.totalCount=0;

		 },
		 // 结账
		 checkout(){
		 	// 有值时才会结账
		 	if(this.totalCount!=0){
		 		this.tableData=[];
		 		this.totalCount=0;
		 		this.totalMoney=0;
		 		this.$message({
		 			message:'结账成功，感谢你的付出',
		 			type:'success'
		 		});

		 	}else{
		 		this.$message.error('不好意思未有订单不能结账，不能结账');
		 	}

		 }
	 }
	}
</script>

<style type="text/css">
	.pos-order{
		background-color: #F9FAFC;
		border-right: 1px solid #C0CCDA;
	}
	.div-btn{
		margin-top: 10px;
	}
	.tittle{
		height:20px;
		border-bottom: 1px solid #D3dce6;
		background-color: #F9FAFC;
		padding: 10px;
		text-align: left;
	}
	.often-goods-list ul li{
		list-style: none;
		float:left;
		border:1px solid #E5E9F2;
		padding: 10px;
		margin:10px;
		background-color: #FFF;
		cursor: pointer;
	}
	.o-price{
		color:#58B7FF;
	}
	.goods-type{
		clear: both;

	}


.cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       /*将鼠标变成手型*/
       cursor: pointer;
 
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 15px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalDiv{
   	background-color: #fff;
   	padding: 10px;
   	border-bottom: 1px solid #D3dce6;
   }
</style>