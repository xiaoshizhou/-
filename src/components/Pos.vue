<template>
<div>

    <el-row  >
      <el-col :span='7' >
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border show-summary style="width: 100%" >
              <el-table-column prop="goodsName" label="商品"  ></el-table-column>
              <el-table-column prop="count" label="量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column  label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small"  @click="addOrderList(scope.row)">增加</el-button>
                </template>

              </el-table-column>
            </el-table>
            <div>
              <span>数量：{{totalCount}}</span>&nbsp; &nbsp;<span>总金额：{{totalMoney}}</span>
            </div>

            <el-button type="warning" >挂单</el-button>
            <el-button type="danger"  @click=" delAllGoods()">删除</el-button>
            <el-button type="success" @click="checkout() ">结账</el-button>
          </el-tab-pane>
          <el-tab-pane label="挂单">
            挂单
          </el-tab-pane>
          <el-tab-pane label="外卖">
            外卖
          </el-tab-pane>
        </el-tabs>
      </el-col>
      <!--商品展示-->
      <el-col :span="15">
        <div class="often-goods" >
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul v-for="oftenGood in oftenGoods">
              <li @click="addOrderList(oftenGood)">
                <span>{{oftenGood.goodsName}}</span>
                <span class="o-price">￥{{oftenGood.price}}元</span>
              </li>
            </ul>
          </div>
        </div>
      </el-col>

      <div class="goods-type">
        <el-tabs>
          <el-tab-pane label="汉堡">
            <ul class='cookList' v-for="goods in type0Goods">
              <li  @click="addOrderList(goods)">
                <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                <span class="foodName">{{goods.goodsName}}</span>
                <span class="foodPrice">￥{{goods.price}}元</span>
              </li>

            </ul>
          </el-tab-pane>
          <el-tab-pane label="小食">
            小食
          </el-tab-pane>
          <el-tab-pane label="饮料">
            饮料
          </el-tab-pane>
          <el-tab-pane label="套餐">
            套餐
          </el-tab-pane>
        </el-tabs>
      </div>
    </el-row>


</div>
</template>

<script>
  import axios from 'axios'
  export default {
      name:'Pos',
    data(){
        return {
          tableData: [],
          oftenGoods:[],
          type0Goods:[],
          totalCount:0,
          totalMoney:0,

        }
    },
    created(){
      axios.get('http://localhost:8080/static/mock.js')
        .then(response=>{

         // console.log(response.data[0]);


          // console.log(this.bookcontent)
          this.oftenGoods=response.data[0].data;
           this.type0Goods=response.data[0].data;
          // this.type1Goods=response.data[1].data;
          // this.type2Goods=response.data[2].data;
          // this.type3Goods=response.data[3].data;
        })
        .catch(error=>{
          console.log(error);
          alert('网络错误，不能访问');
        })
    },
    methods:{
      //添加订单列表的方法
      addOrderList(goods){
        this.totalCount=0; //汇总数量清0
        this.totalMoney=0;
        let isHave=false;
        //判断是否这个商品已经存在于订单列表
        for (let i=0; i<this.tableData.length;i++){
          console.log(this.tableData[i].goodsId);
          if(this.tableData[i].goodsId==goods.goodsId){
            isHave=true; //存在
          }
        }
        //根据isHave的值判断订单列表中是否已经有此商品
        if(isHave){
          //存在就进行数量添加
          let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
          arr[0].count++;
          //console.log(arr);
        }else{
          //不存在就推入数组
          let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
          this.tableData.push(newGoods);
        }
        //进行数量和价格的汇总计算
        this.tableData.forEach((element) => {
          this.totalCount+=element.count;
          this.totalMoney=this.totalMoney+(element.price*element.count);
        });
      },
      delSingleGoods(goods){
        console.log(goods);
        this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
      },
      getAllMoney(){
        this.totalCount=0;
        this.totalMoney=0;
        if(this.tableData){
          this.tableData.forEach((element) => {
            this.totalCount+=element.count;
            this.totalMoney=this.totalMoney+(element.price*element.count);
          });
        }
      },
  delAllGoods() {
    this.tableData = [];
    this.totalCount = 0;
    this.totalMoney = 0;
  },
      checkout() {
        if (this.totalCount!=0) {
          this.tableData = [];
          this.totalCount = 0;
          this.totalMoney = 0;
          this.$message({
            message: '结账成功，感谢你又为店里出了一份力!',
            type: 'success'
          });
        }else{
          this.$message.error('不能空结。老板了解你急切的心情！');
        }
      }
    }


  }
</script>

<style scoped>
  .title{
    height: 20px;
    border-bottom:1px solid #D3DCE6;
    background-color: #F9FAFC;
    padding:10px;
  }
  .often-goods-list ul li{
    list-style: none;
    float:left;
    border:1px solid #E5E9F2;
    padding:10px;
    margin:5px;
    background-color:#fff;
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

    overflow: hidden;
    background-color:#fff;
    padding: 2px;
    float:left;
    margin: 2px;
  }
  .cookList li span{
    display: block;
    float:left;
  }
  .foodImg{
    width: 40%;
  }
  .foodName{
    font-size: 18px;
    padding-left: 10px;
    color:brown;
  }
  .foodPrice{
    font-size: 16px;
    padding-left: 10px;
    padding-top:10px;
  }
</style>
