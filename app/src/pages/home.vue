<template>
  <div>
    <el-container>
      <el-header height="50px" v-show="!meta.headerHide">
        <div class="user-info">
          <span class="iconfont iconzuojiantou" @click="back" v-show="meta.goBackShow"></span>
          <span
            v-for="(item, index) in titleList"
            :key="index"
            @click="toPage(item)"
            :class="{active: item.active }">
            {{item.titleName}}
          </span>
        </div>
      </el-header>

      <el-container>
        <el-main :class="{'main-class1' : meta.headerHide, 'main-class2' : !meta.headerHide}">
          <router-view></router-view>
        </el-main>
      </el-container>

      <el-footer v-show="meta.footerShow">
        <!-- 店长 -->
        <div @click="$router.push({path: '/shop-order'})" v-show="this.user.role ==='1'">
          <img src="../assets/u1075.png">
          <div :style="{color: currentPath === '/shop-order' ? '#C39862' : '#333333'}">订单</div>
        </div>

        <div @click="$router.push({path: '/shop-driver'})" v-show="this.user.role ==='1'">
          <img src="../assets/u1080.png">
          <div :style="{color: currentPath === '/shop-driver' ? '#C39862' : '#333333'}">司机</div>
        </div>

                <div @click="$router.push({path: '/shop-my'})" v-show="this.user.role ==='1'">
          <img src="../assets/u184.png">
          <div :style="{color: currentPath === '/shop-my' ? '#C39862' : '#333333'}">我的</div>
        </div>

        <!-- 司机 -->
        <div @click="$router.push({path: '/driver-info'})" v-show="this.user.role ==='4'">
          <img src="../assets/u169.png">
          <div :style="{color: currentPath === '/driver-info' ? '#C39862' : '#333333'}">门店</div>
        </div>

        <div @click="$router.push({path: '/driver-my'})" v-show="this.user.role ==='4'">
          <img src="../assets/u184.png">
          <div :style="{color: currentPath === '/driver-my' ? '#C39862' : '#333333'}">我的</div>
        </div>

        <!-- 普通用户 -->
        <div @click="$router.push({path: '/comm-home'})" v-show="this.user.role ==='3'">
          <img v-show="currentPath !== '/comm-home'" src="../assets/home2.png" alt="">
          <img v-show="currentPath === '/comm-home'" src="../assets/home.png" alt="">
          <div :style="{color: currentPath === '/comm-home' ? '#C39862' : '#333333'}">首页</div>
        </div>
        <div @click="$router.push({path: '/comm-classify'})" v-show="this.user.role ==='3'">
          <img v-show="currentPath !== '/comm-classify'" src="../assets/classify2.png" alt="">
          <img v-show="currentPath === '/comm-classify'" src="../assets/classify.png" alt="">
          <div :style="{color: currentPath === '/comm-classify' ? '#C39862' : '#333333'}">分类</div>
        </div>
        <div @click="$router.push({path: '/shop-car'})" v-show="this.user.role ==='3'">
          <img v-show="currentPath !== '/shop-car'" src="../assets/shop_car2.png" alt="">
          <img v-show="currentPath === '/shop-car'" src="../assets/shop_car.png" alt="">
          <div :style="{color: currentPath === '/shop-car' ? '#C39862' : '#333333'}">购物车</div>
        </div>
        <div @click="$router.push({path: '/mine'})" v-show="this.user.role ==='3'">
          <img v-show="currentPath !== '/mine'" src="../assets/mine2.png" alt="">
          <img v-show="currentPath === '/mine'" src="../assets/mine.png" alt="">
          <div :style="{color: currentPath === '/mine' ? '#C39862' : '#333333'}">我的</div>
        </div>
      </el-footer>
    </el-container>
  </div>
</template>

<script>
import req from '@/api/home.js'
export default {
  name: 'home',
  data () {
    return {
      user:{},
      mainStyle: {
      }
    }
  },
  computed: {
    titleList () {
      return this.$route.meta.title
    },
    goBackBtn () {
      return this.$route.meta.goBack
    },
    meta () {
      return this.$route.meta
    },
    currentPath () {
      return this.$route.path
    },
    userType(){
      return this.$store.state.userType
    }
  },
  methods: {
    back () {
      if(this.user.role ==='1' && this.currentPath === '/change-password'){
        this.$router.push({path: '/shop-my'})
      }else if(this.user.role ==='4' && this.currentPath === '/change-password'){
         this.$router.push({path: '/driver-my'})
         
      }else if(this.user.role ==='1' && this.currentPath === '/order-detail'){
        this.$router.push({path: '/shop-order'})
      }else if(this.user.role ==='1' && this.currentPath === '/comm-detail'){
           this.$router.push({path: '/shop-order'})
      }
      else{
              this.$router.push({path: this.goBackBtn})
      }
    },
    toPage (data) {
      if (data.toPath !== this.$route.path) {
        this.$router.push({path: data.toPath})
      }
    }
  },
  mounted(){
    req('findUser').then(data =>{
      this.user=data.data
      console.log(this.user)
    })
  }
}
</script>

<style lang="scss" scoped>
.main-class1 {
  position: absolute;
  top: 0;
  bottom: 60px;
}

.main-class2 {
  position: absolute;
  top: 50px;
  bottom: 0;
}

.el-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: rgb(242,242,242);

  .router-link-active {
    font-size: 25px;
    color: #409EFF;
    text-decoration: none;
  }

  .user-info {
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 50px;
    font-size: 20px;
    width: 100%;

    .iconzuojiantou {
      position: absolute;
      top: 50%;
      left: 10px;
      transform: translate(0, -50%);
    }

    span {
      margin: 0 10px;
    }

    .active {
      color: #C39862;
    }
  }
}

.el-main {
  padding: 0;
  width: 100%;
}

.el-footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 0;
  display: flex;
  height: 60px;
  justify-content: space-between;
  align-items: center;
  background: #ffffff;
  border-top: 1px solid #000;

  >div {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    font-size: 14px;
    color: #333333;

    img {
      width: 30px;
      height: 30px;
    }

    div {
      position: relative;
      top: 3px;
    }
  }
}
</style>
