<template>
  <div class="pos">
    <el-row>
      <!-- 左侧订单栏 -->
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs style="padding-left:5px;">
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width:100%">
              <!-- 表格的每一列 -->
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <!-- fixed="right" 固定这一列一直显示 -->
              <el-table-column label="操作" width="100" fixed="right">
                <!-- scope="scope" 模板作用域，通过这个可以拿到这一行的作用域 -->
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="delSignleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <!-- 下面的小额总计 -->
            <div class="totalDiv">
              <small>数量：</small>
              {{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp;
              <small>金额：</small>
              {{totalMoney}}
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGoods()">删除</el-button>
              <el-button type="success" @click="checkout">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
      </el-col>
      <!-- 右侧商品栏 -->
      <el-col :span="17">
        <!-- 常用商品 -->
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="goods in oftenGoods" :key="goods.id" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div>
        <!-- 右侧类别 -->
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type0Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%" />
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class="cookList">
                <li v-for="goods in type1Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                  <span class="foodImg">
                    <img :src="goods.goodsImg" width="100%" />
                  </span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">饮料</el-tab-pane>
            <el-tab-pane label="套餐">套餐</el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
//引入axios
import axios from "axios";
export default {
  name: "pos",
  created() {
    //http://old.jspag.com/DemoApi/oftenGoods.php
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(response => {
        console.log(response);
        this.oftenGoods = response.data;
      })
      .catch(error => {
        console.log(error);
        // alert('网络错误，不能访问');
      });
    //读取分类商品列表
    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(response => {
        console.log(response);
        //this.oftenGoods=response.data;
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      })
      .catch(error => {
        console.log(error);
        // alert('网络错误，不能访问');
      });
  },
  //所有的虚拟dom加载完成时调用的方法
  mounted() {
    //用js代码设置订单栏的高度
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  methods: {
    //添加商品到订单
    addOrderList(goods) {
      this.totalMoney = 0;
      this.totalCount = 0;
      //判断商品是否已经存在于订单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据判断的值编写业务逻辑
      if (isHave) {
        //改变列表中商品的数量
        let arr = this.tableData.filter(a => a.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        //不存在就推入数组
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      //计算数量和金额
      this.getAllMoney();
    },

    //删除单个商品
    delSignleGoods(goods) {
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      //计算数量和金额
      this.getAllMoney();
    },
    //删除所有商品
    delAllGoods() {
      this.tableData = [];
      this.totalMoney = 0;
      this.totalCount = 0;
    },
    //模拟结账
    checkout(){
      if (this.totalCount != 0) {
        this.delAllGoods();
        this.$message({
          message:'结账成功，感谢你又为店里出了一份力!',
          type:'success'
        });
      } else {
        this.$message.error('不能空结，老板了解你急切的心情！');
      }
    },
    //汇总数量和金额
    getAllMoney() {
      this.totalMoney = 0;
      this.totalCount = 0;
      if (this.tableData) {
        //计算数量和金额
        this.tableData.forEach(element => {
          this.totalCount += element.count;
          this.totalMoney += element.price * element.count;
        });
      }
    }
  },
  data() {
    return {
      // 汇总金额
      totalMoney: 0,
      // 汇总数量
      totalCount: 0,
      tableData: [],
      oftenGoods: [
        {
          goodsId: 1,
          goodsName: "香辣鸡腿堡",
          price: 18
        },
        {
          goodsId: 2,
          goodsName: "田园鸡腿堡",
          price: 15
        },
        {
          goodsId: 3,
          goodsName: "和风汉堡",
          price: 15
        },
        {
          goodsId: 4,
          goodsName: "快乐全家桶",
          price: 80
        },
        {
          goodsId: 5,
          goodsName: "脆皮炸鸡腿",
          price: 10
        },
        {
          goodsId: 6,
          goodsName: "魔法鸡块",
          price: 20
        },
        {
          goodsId: 7,
          goodsName: "可乐大杯",
          price: 10
        },
        {
          goodsId: 8,
          goodsName: "雪顶咖啡",
          price: 18
        },
        {
          goodsId: 9,
          goodsName: "大块鸡米花",
          price: 15
        },
        {
          goodsId: 20,
          goodsName: "香脆鸡柳",
          price: 17
        }
      ],
      type0Goods: [
        {
          goodsId: 1,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "香辣鸡腿堡",
          price: 18
        },
        {
          goodsId: 2,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "田园鸡腿堡",
          price: 15
        },
        {
          goodsId: 3,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "和风汉堡",
          price: 15
        },
        {
          goodsId: 4,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "快乐全家桶",
          price: 80
        },
        {
          goodsId: 7,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "可乐大杯",
          price: 10
        },
        {
          goodsId: 8,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "雪顶咖啡",
          price: 18
        },
        {
          goodsId: 9,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "大块鸡米花",
          price: 15
        },
        {
          goodsId: 20,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "香脆鸡柳",
          price: 17
        }
      ],
      type1Goods: [
        {
          goodsId: 5,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "脆皮炸鸡腿",
          price: 10
        },
        {
          goodsId: 6,
          goodsImg:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1569416956318&di=993ea7b642104303384477da0eed7a8d&imgtype=0&src=http%3A%2F%2Fp1.so.qhmsg.com%2Ft01d1ee4fb639ea3f46.jpg",
          goodsName: "魔法鸡块",
          price: 20
        }
      ],
      type2Goods: [],
      type3Goods: []
    };
  },
  components: {}
};
</script>

<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods {
  overflow: hidden;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
  cursor: pointer;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  padding-left: 10px;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
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
.totalDiv {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #d3dce6;
}
</style>
