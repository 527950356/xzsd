<template>
  <div>
    <div class="content">
        <div class="con-top" v-for="item in orderList" :key="item.id">
            <div class="top-item">
                <div class="top-img-con">
                    <img src="../../assets/卡车.png" :width="item.width">
                </div>
                
                <span>取货门店：{{item.storeName}}</span>
            </div>
            <div class="top-item">
             <div class="top-img-con">
                    <img src="../../assets/position2.png" :width="item.width">
                </div>
                <span>{{item.address}}</span>
            </div>
        </div>
        <div class="line"></div>
        <div class="order-item" v-for="item of orderList" :key="item.id">
            <div class="item-center">
                <div class="text"  v-for="item of item.goodsList" :key="item.id">
                    <div class="haha">
            <div class="img-con">
                    <img :src="item.goodsImagePath"   @click="toDetailPage(item)">
                </div>
                <div class="text-con">{{item.goodsIntroduce}}</div>
                    </div>

                    <div class="props-con">{{item.prop}}</div>
                    <div class="price-con">
                        <span>￥</span>
                        <span>{{item.goodsPrice}} </span>
                        <span>x{{item.goodsCount}}</span>
                    </div>
                </div>
            </div>
            <div class="item-bottom">
                <span>共{{item.pucharNum}}件商品，合计￥</span>
                <span>{{item.sumPrice}}</span>
            </div>
        </div>
    </div>
    <div class="order-msg" v-for="item of orderList" :key="item.id">
        <ul>
            <li>订单信息</li>
            <li>订单编号：</li>
            <li>创建时间：</li>
            <li>订单状态：</li>
        </ul>
        <ul>
            <li></li>
            <li>{{item.orderCode}}</li>
            <li>{{item.payTime}}</li>
            <li class="active">{{item.orderActive ==='0'?'已下单':
                item.orderActive ==="1"?'已取消':
                item.orderActive ==="2"?'已到货':
                item.orderActive ==="3"?'已取货':
                item.orderActive ==="4"?'已完成未评价':'已完成已评价'}}</li>
        </ul>

         <div  class="footer">
       <el-button round v-show="item.orderActive == '0'&&shopinfo.data.role =='3'" @click="change(item)">
        <span>取消订单</span>     
       </el-button>
       <el-button round class="change" v-show="item.orderActive == '0'&&shopinfo.data.role =='1'" @click="change3(item)"><span>已发货</span></el-button>
       <el-button round class="change" v-show="item.orderActive == '2'" @click="change2(item)"><span>确认收货</span></el-button>
       <el-button round class="change" v-show="item.orderActive == '4'"  @click.stop="evaluate(item)"><span>评价</span></el-button>
    </div>
    </div>
   
  </div>
</template>

<script>
import req from '@/api/order-detail.js'
export default {
  name: 'order-detail',
  data () {
    return {
        orderCode:{},
        orderTye:1,
        shopinfo:{},
    
      orderList: [
        // {
        //   id: '001',
        //   adv: '一生自在季羡林的自在智慧（午静携侣寻野菜，黄昏抱猫看夕阳！金庸、贾平凹...）',
        //   img: require('../../assets/book1.jpg'),
        //   prop: '重量：0.32kg 系列：一生自在系列',
        //   price: '42.80',
        //   number: '1',
        //   count: '42.80'
        // }
      ],
      msgList: [
        // '2020020713270034',
        // '2020-02-11 11:54:01',
        // '已付款'
      ]
    }
  },
  mounted(){
      this.orderCode =JSON.parse(sessionStorage.getItem('orderCode'))
       this.shopinfo =JSON.parse(sessionStorage.getItem('shopinfo'))
       console.log(this.shopinfo)
    req('orderDetail',{
        orderCode:this.orderCode.orderCode
    }).then(data =>{
        this.orderList=data.data
    })
  },
  methods:{
       toDetailPage (data) {
      this.$router.push({path: '/comm-detail'})
        sessionStorage.setItem('goodsinfo',JSON.stringify(data))
    },

        change(data){
            req('updateOrderActive',{
                orderCode:data.orderCode,
                orderActive:1,
                version:data.version
            }).then(data =>{
                this.$message.success(data.msg)
                 this.$router.push({path: '/order-list'})
            })
        },
         change2(data){
            req('updateOrderActive',{
                orderCode:data.orderCode,
                orderActive:4,
                version:data.version
            }).then(data =>{
                this.$message.success(data.msg)
                 this.$router.push({path: '/order-list'})
            })
        },
         change3(data){
            req('updateOrderActive',{
                orderCode:data.orderCode,
                orderActive:2,
                version:data.version
            }).then(data =>{
                this.$message.success(data.msg)
               this.$router.push({path: '/shop-order'})
            })
        }, 
        evaluate (data) {
			console.log(data)
			this.$router.push({path: '/order-evaluate'})
        },
  }
}
</script>

<style lang="scss" scoped>
  .content {
      width: 95%;
      margin: 12px auto 12px;
      background-color: white;
      border-radius: 8px;
      padding: 13px 13px 0;
      box-sizing: border-box;
      .con-top {
          width: 100%;
          .top-item {
              display: flex;
              padding: 5px 0 22px;
              .top-img-con {
                  width: 30px;
                  text-align: center;
              }
              span {
                  font-size: 15px;
              }
          }
      }
      .line {
          width: 100%;
          height: 1px;
          background-color: rgb(204,204,204);
          margin: 5px 0;
      }
      .order-item {
          width: 95%;
          margin: 0 auto;
          background-color: white;
          border-radius: 8px;
          padding: 10px 15px;
          box-sizing: border-box;
          .item-center {
              width: 100%;
              display: flex;
              box-sizing: border-box;
              padding-top: 5px;
              .haha{
                  display:flex;
              }
              .text {
                  flex: 2.2;

                   .img-con {
                //   flex: 1;
                            img {
                      width: 84px;
                  }
                   }
                  
                  .text-con {
                      font-size: 15px;
                      line-height: 22px;
                      overflow : hidden;
                      text-overflow: ellipsis;
                      display: -webkit-box;
                      -webkit-line-clamp: 2;
                      -webkit-box-orient: vertical;
                  }
                  .props-con {
                      font-size: 13.5px;
                      line-height: 26px;
                      color: rgb(168,168,168);
                  }
                  .price-con {
                      text-align: right;
                      line-height: 26px;
                      span:nth-child(1) {
                          font-size: 11px;
                          color: rgb(242,0,0);
                      }
                      span:nth-child(2) {
                          font-size: 16px;
                          color: rgb(242,0,0);
                      }
                      span:nth-child(3) {
                          font-size: 11px;
                          color: rgb(168,168,168);
                      }
                  }
              }
          }
          .item-bottom {
              width: 100%;
              line-height: 40px;
              text-align: right;
              padding-top: 5px;
              span:nth-child(1) {
                  font-size: 15px;
              }
              span:nth-child(2) {
                  font-size: 20px;
              }
          }
      }
  }
  .order-msg {
      width: 95%;
      background-color: white;
      box-sizing: border-box;
      padding: 8px 0 12px;
      margin: 0 auto;
      display: flex;
      border-radius: 8px;
      ul:nth-child(1) {
          margin: 0;
          padding: 0;
          flex: 1;
          li:nth-child(1) {
              margin: 0;
              padding: 0 0 0 25%;
              box-sizing: border-box;
              list-style: none;
              font-size: 16.5px;
              font-weight: bold;
              line-height: 42px;
          }
          li:nth-child(2) {
              margin: 0;
              padding: 0 0 0 25%;
              box-sizing: border-box;
              list-style: none;
              font-size: 14.5px;
              line-height: 30px;
          }
          li:nth-child(3) {
              margin: 0;
              padding: 0 0 0 25%;
              box-sizing: border-box;
              list-style: none;
              font-size: 14.5px;
              line-height: 30px;
          }
          li:nth-child(4) {
              margin: 0;
              padding: 0 0 0 25%;
              box-sizing: border-box;
              list-style: none;
              font-size: 14.5px;
              line-height: 30px;
          }
      }
      ul:nth-child(2) {
          margin: 0;
          padding: 0;
          flex: 2;
          li:nth-child(1) {
              margin: 0;
              padding: 0;
              list-style: none;
              height: 42px;
          }
          li:nth-child(2) {
              margin: 0;
              padding: 0;
              list-style: none;
              font-size: 14.5px;
              line-height: 30px;
          }
          li:nth-child(3) {
              margin: 0;
              padding: 0;
              list-style: none;
              font-size: 14.5px;
              line-height: 30px;
          }
          li:nth-child(4) {
              margin: 0;
              padding: 0;
              list-style: none;
              font-size: 14.5px;
              line-height: 30px;
          }
          .active {
              color: rgb(195,152,98);
          }
      }
      
  }
  .footer{
         position: absolute;
    bottom: 15px;
    right: 29px;
     .el-button {
          border: 2px solid #C79F6C;
          height: 33px;
          span{
              position: relative;
              bottom: 13px;
              color: #C79F6C;
          }
     }
      }
</style>
