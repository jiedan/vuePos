<template>
  <div class="pos">
    <el-row>

      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            点餐
            <el-table border style="width: 100%" :data="tableData">
              <el-table-column prop="goodsName" label="商品"></el-table-column>
              <el-table-column prop="count" label="量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column fixed="right" label="操作" width="100">
                <template scope="scope">
                  <el-button type="text" size="small" @click="deleteSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="totalDiv">
              <small>数量：</small>
              {{ totalCount }} &nbsp;&nbsp;&nbsp;
              <small>金额：</small>
              {{ totalMoney }}元
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="deleteAllGoods">删除</el-button>
              <el-button type="success" @click="checkout">结账</el-button>
            </div>

          </el-tab-pane>

          <el-tab-pane label="挂单">挂单</el-tab-pane>

          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
      </el-col>

      <el-col :span="17">

        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="oftenGood in oftenGoods" @click="addOrderList(oftenGood)">
                <span>{{ oftenGood.goodsName }}</span>
                <span class="o-price">￥{{ oftenGood.price }}元</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <ul class='cookList'>
                <li v-for="type0Good in type0Goods" @click="addOrderList(type0Good)">
                  <span class="foodImg"><img :src="type0Good.goodsImg" width="100%"></span>
                  <span class="foodName">{{ type0Good.goodsName }}</span>
                  <span class="foodPrice">￥{{ type0Good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class='cookList'>
                <li v-for="type1Good in type1Goods" @click="addOrderList(type1Good)">
                  <span class="foodImg"><img :src="type1Good.goodsImg" width="100%"></span>
                  <span class="foodName">{{ type1Good.goodsName }}</span>
                  <span class="foodPrice">￥{{ type1Good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class='cookList'>
                <li v-for="type2Good in type2Goods" @click="addOrderList(type2Good)">
                  <span class="foodImg"><img :src="type2Good.goodsImg" width="100%"></span>
                  <span class="foodName">{{ type2Good.goodsName }}</span>
                  <span class="foodPrice">￥{{ type2Good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class='cookList'>
                <li v-for="type3Good in type3Goods" @click="addOrderList(type3Good)">
                  <span class="foodImg"><img :src="type3Good.goodsImg" width="100%"></span>
                  <span class="foodName">{{ type3Good.goodsName }}</span>
                  <span class="foodPrice">￥{{ type3Good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
          </el-tabs>
        </div>

      </el-col>
    </el-row>
  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    name: 'pos',
    created: function () {
      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
        .then(response=>{
        console.log(response);
        this.oftenGoods=response.data;
      })
      .catch(error=>{
        console.log(error);
        alert('网络错误，不能访问');
      })

      axios.get('http://jspang.com/DemoApi/typeGoods.php').then(response=>
      {
        console.log(response)
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      }
      ).
      catch(error=>
      {
        alert('网络错误，请稍后再试！')
      }
      )
    },
    data: function () {
      return {
        tableData: [],
        oftenGoods: [],
        type0Goods: [],
        type1Goods: [],
        type2Goods: [],
        type3Goods: [],
        totalCount: 0,
        totalMoney: 0
      }
    },
    mounted: function () {
      var orderHeight = document.body.clientHeight;
      document.getElementById("order-list").style.height = orderHeight + 'px';
    },
    methods: {
      addOrderList: function (goods) {
        var isHave = false;
        var haveGoods = {};
        this.tableData.forEach(function (item) {
          if (item.goodsId == goods.goodsId) {
            isHave = true;
            haveGoods = item;
          }
        });
        if (isHave) {
          ++haveGoods.count
        } else {
          var newGoods = {goodsId: goods.goodsId, goodsName: goods.goodsName, price: goods.price, count: 1};
          this.tableData.push(newGoods)
        }

        // 汇总
        this.getAllMoney()
      },

      deleteSingleGoods:function(goods){
        this.tableData =  this.tableData.filter(item =>{
          return item.goodsId != goods.goodsId
        })
        this.getAllMoney()
      },
      getAllMoney:function(){
        this.totalCount = 0;
        this.totalMoney = 0;
        // 汇总
        this.tableData.forEach(item => {
          this.totalCount = item.count +  this.totalCount;
          this.totalMoney = item.count*item.price +  this.totalMoney;

        });
      },
      deleteAllGoods:function(){
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
      },
      checkout:function(){
        if(this.totalCount != 0){
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
  .pos-order {
    border-right: 1px solid #c0ccda;
    height: 100%;
  }

  #order-list {
    background: #f9fafc;
  }

  .totalDiv {
    border-bottom: 1px solid #c0ccda;
    padding: 10px;
    text-align: center;
    background: #fff
  }

  .div-btn {
    margin-top: 10px;
    text-align: center;
  }

  .often-goods {
    overflow: auto;
    border-bottom: 1px solid #c0ccda;
    padding-bottom: 10px;
    background: #f9fafc;
  }

  .title {
    border-bottom: 1px solid #D3DCE6;
    padding: 10px;
    height: 20px;
  }

  .often-goods-list ul li {
    float: left;
    list-style: none;
    margin: 5px;
    border: 1px solid #E5E9F2;
    padding: 10px;
    background-color: #fff;
  }

  .o-price {
    color: #58B7FF;
  }

  .cookList li {
    float: left;
    list-style: none;
    margin: 2px;
    border: 1px solid #E5E9F2;
    padding: 2px;
    width: 23%;
    background-color: #fff;
    overflow: hidden;
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
</style>
