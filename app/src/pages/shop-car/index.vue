<template>
  <div class="container">
    <ul class="book-list">
      <li class="book-list-item" v-for="(item,index) in list" :key="index">

        <!-- 选择按钮 -->
        <div class="select-btn-box">
          <div>
            <input type="checkbox" @change="inputChang(item)">
            <div class="action" :style="{display:item.checked ? 'block' : 'none'}"></div>
          </div>
        </div>
        <!-- 书本信息 -->
        <div class="book-count-info">
          <img :src="item.goodsImagePath" alt=""   @click="toDetailPage(item)">
          <div>
            <div>{{item.goodsName}}</div>
            <div></div>
            <div>
              <span>￥{{item.goodsPrice}}</span>
              <div class="change-count-box">
                <div @click="delele(item)">-</div>
                <div>{{item.goodsCount}}</div>
                <div @click="add(item)">+</div>
              </div>
            </div>
          </div>
        </div>
      </li>

    </ul>

    <div class="close-count">
      <div>
        <div>
          <input type="checkbox" @change="allIputChage()">
          <div class="action" :style="{display:this.allChecked ? 'block' : 'none'}"></div>
        </div>
        <span>全选</span>
      </div>

      <div>
        <div>
          <b>合计:</b>
          <span>{{totalPrice}}</span>
        </div>

        <button @click="addorder">结算</button>
        <button @click="deletegoods">删除</button>
      </div>
    </div>
  </div>
</template>

<script>
import req from '@/api/shop-car.js'
export default {
  name: 'shop-car',
  data () {
    return {
            list: [],
            totalPrice:0,
            allChecked:false,
            shopinfo:{}

    }
  },
  mounted(){
        let list =[
        ]
        req('listShopCar').then(data =>{
          this.list=data.data
          console.log(this.list)
        })
                this.list = list.map(item =>{
          return Object.assign(item,{checked:false})
        })
        this.shopinfo =JSON.parse(sessionStorage.getItem('shopinfo'))
        console.log(this.shopinfo)
  },
  methods:{
     toDetailPage (data) {
      this.$router.push({path: '/comm-detail'})
        sessionStorage.setItem('goodsinfo',JSON.stringify(data))
    },

    allIputChage(){
      this.allChecked=!this.allChecked

      this.list.forEach(item =>{
        item.checked=this.allChecked
      })
    this.totalPrice = this.getTotal()
    },

    inputChang(item){
      item.checked = !item.checked

      for(let i=0;i<this.list.length;i++){
        if(this.list[i].checked===false){
          this.allChecked =false
          break
        }
        else{
          this.allChecked = true
        }
      }
      this.totalPrice = this.getTotal()
    },

    add(data){
        data.goodsCount = parseFloat(data.goodsCount) +1
          this.totalPrice = this.getTotal()
         
      req('updateShopCar',{
        GoodsCount:data.goodsCount,
        shopCarCode:data.shopCarCode
      }).then(req =>{
        console.log(req.code)
        if(req.code ==='500'){
          
          this.$message.error(req.msg);
          data.goodsCount = parseFloat(data.goodsCount) -1
        }
      })
         
    },
    delele(data){
       if(data.goodsCount>1){
        data.goodsCount = parseFloat(data.goodsCount) - 1
      }
      this.totalPrice = this.getTotal()
       req('updateShopCar',{
        GoodsCount:data.goodsCount,
        shopCarCode:data.shopCarCode
      }).then(data =>{

      })
      
    },
    getTotal(){
      let total = 0

      this.list.forEach(item =>{
        if(item.checked){
          total = total + parseFloat(item.goodsCount *item.goodsPrice)
        }
      })
      return parseFloat(total.toFixed(2))
    },
    deletegoods(){
       this.list.forEach(item =>{
        if(item.checked){
          req('deleteShopCar',{
            shopCarCodeList:item.shopCarCode
          }).then(data =>{
            this.$message.success(data.msg)
            req('listShopCar').then(data =>{
          this.list=data.data
        })
          })
        }
      })
    },
    addorder(){
                this.$confirm('结算').then(() =>{
              this.list.forEach(item =>{
        if(item.checked){
       req('addOrder',{
         goodsCodeList:item.goodsCode,
         goodsCountList:item.goodsCount,
         goodsPriceList:item.goodsPrice,
        storeCode:this.shopinfo.data.storeCode,
        shopCarCodeList:item.shopCarCode
      }).then(data =>{
            this.$message.success(data.msg)
                  req('listShopCar').then(data =>{
          this.list=data.data
        })
          })
        }
      })
    })
    }

  }
}
</script>


<style lang="scss" scoped>
.container {
  padding-bottom: 110px;
}
  .book-list {
    width: 100%;
    // position: absolute;
    // left: 50%;
    // bottom: 55px;
    overflow: auto;
    // transform: translate(-50%, 0);

    li {
      display: flex;
      justify-content: flex-start;
      align-items: center;
      width: 100%;
      height: 150px;
      background: #fff;
      border-radius: 10px;
      padding: 10px 10px;
      box-sizing: border-box;
      margin-bottom: 10px;

      .select-btn-box {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 40px;
        height: 100%;

        >div {
          position: relative;
          width: 20px;
          height: 20px;
          border: 2px solid #ddd;
          border-radius: 50%;

          input {
            position: absolute;
            left: 50%;
            top: 50%;
            transform: translate(-50%, -50%);
            padding: 0;
            margin: 0;
            width: 15px;
            height: 15px;
            z-index: 1;
            opacity: 0;
          }

          .action {
            display: none;
            position: absolute;
            width: 15px;
            height: 15px;
            border-radius: 50%;
            background: rgb(197, 156, 104);
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            z-index: 0;
          }
        }
      }

      .book-count-info {
        display: flex;
        justify-content: flex-start;
        align-items: center;
        flex: 1;
        height: 100%;

        >img {
          height: 80%;
          vertical-align: middle;
          align-self: center;
        }

        >div {
          > div:first-child {
            font-size: 14px;
            width: 100%;
            height: 40px;
            padding-left: 10px;
            box-sizing: border-box;
          }

          > div:nth-child(2) {
            font-size: 14px;
            width: 100%;
            height: 30px;
            padding-left: 10px;
            box-sizing: border-box;
            color: #ddd;
          }

          > div:nth-child(3) {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 14px;
            width: 100%;
            height: 30px;
            padding-left: 10px;
            box-sizing: border-box;

            .change-count-box {
              display: flex;
              height: 20px;
              margin-left: 71px;

              div:first-child {
                width: 30px;
                height: 20px;
                text-align: center;
                line-height: 20px;
                border: 1px solid #ddd;
              }

              div:nth-child(2) {
                width: 50px;
                height: 20px;
                text-align: center;
                line-height: 20px;
                border: 1px solid #ddd;
                border-left: none;
                border-right: none;
              }

              div:nth-child(3) {
                width: 30px;
                height: 20px;
                text-align: center;
                line-height: 20px;
                border: 1px solid #ddd;
              }
            }
          }
        }
      }
    }
  }

.close-count {
  position: fixed;
  bottom: 60px;
  width: 100%;
  height: 50px;
  background: #fff;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 10px;
  box-sizing: border-box;

  div:first-child {
    display: flex;
    height: 50px;
    align-items: center;

    div {
      position: relative;
      width: 20px;
      height: 20px;
      border: 2px solid #ddd;
      border-radius: 50%;
      margin-right: 10px;

      input {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        padding: 0;
        margin: 0;
        width: 15px;
        height: 15px;
        z-index: 1;
        opacity: 0;
      }

      .action {
        display: none;
        position: absolute;
        width: 15px;
        height: 15px;
        border-radius: 50%;
        background: rgb(197, 156, 104);
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        z-index: 0;
      }
    }
  }

  div:nth-child(2) {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    height: 50px;

    div:first-child {
      height: 50px;
      line-height: 50px;
      font-size: 14px;

      span {
        font-size: 18px;
        color: red;
      }
    }

    button {
      width: 80px;
      height: 40px;
      background: rgb(197, 156, 104);
      color: #fff;
      outline: none;
      line-height: 40px;
      text-align: center;
      border: none;
      border-radius: 10px;
      margin-left: 20px;
    }

    button:nth-child(3) {
      background: red;
    }
  }
}

</style>
