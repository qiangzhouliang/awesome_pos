<template>
 <div class="pos">
   <el-row>
     <el-col :span='7' class="pos-order" id="order-list">
       <el-tabs style="padding-left:5px;">
         <el-tab-pane label="点餐">
           <el-table :data = "tableData" border style="width:100%">
             <el-table-column prop="goodsName" label="商品名称"></el-table-column>
             <el-table-column prop="count" label="数量" width="50"></el-table-column>
             <el-table-column prop="price" label="金额" width="70"></el-table-column>
             <!-- fixed="right" 固定这一列一直显示 -->
             <el-table-column  label="操作" width="100" fixed="right">
               <template>
                 <el-button type="text" size="small">删除</el-button>
                 <el-button type="text" size="small">增加</el-button>
               </template>
             </el-table-column>
           </el-table>
           <div class="div-btn">
             <el-button type="warning">挂单</el-button>
             <el-button type="danger">删除</el-button>
             <el-button type="success">结账</el-button>
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
     <el-col>
       我是商品栏
     </el-col>
   </el-row>
 </div>
</template>

<script>
export default {
 name: "pos",
 //所有的虚拟dom加载完成时调用的方法
 mounted() {
   //用js代码设置订单栏的高度
   var orderHeight = document.body.clientHeight;
   document.getElementById('order-list').style.height=orderHeight+'px';
 },
 data() {
  return {
    tableData: [{
          
          goodsName: '可口可乐',
          price: 8,
          count:1
        }, {
          
          goodsName: '香辣鸡腿堡',
          price: 15,
          count:1
        }, {
         
          goodsName: '爱心薯条',
          price: 8,
          count:1
        }, {
         
          goodsName: '甜筒',
          price: 8,
          count:1
        }]
  }
 },
 components: {

 }
}
</script>

<style scoped>
  .pos-order {
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
  }
  .div-btn {
    margin-top: 10px;
  }
</style>
