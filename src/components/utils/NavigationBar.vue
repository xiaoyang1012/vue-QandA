<template>
  <div class="nav">
    <div class="nav-search">
      <search-frame/>
    </div>
    <el-menu :default-active="navActiveIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect">
      <span class="left-nav">
        <router-link to='/'>
          <el-menu-item index="1" @click="navChangeIndex('1')"><i class="iconfont icon-home"></i> 主页</el-menu-item>
        </router-link>
        <router-link to='/questions'>
          <el-menu-item index="2" @click="navChangeIndex('2')"><i class="iconfont icon-focus"></i> 发现</el-menu-item>
        </router-link>
        <router-link to='/topics'>
          <el-menu-item index="3" @click="navChangeIndex('3')"><i class="iconfont icon-link"></i> 话题</el-menu-item>
        </router-link>
          <el-menu-item index="4" @click="msg()"><i class="iconfont icon-message"></i> 消息</el-menu-item>
      </span>
      <span class="right-nav">
        <el-submenu index="5" v-if="islogin">
          <template slot="title">
            
            <div v-if="!hasimg">{{nowuser.name}}</div>
             <img v-else class="profile-img" style="width: 32px; height: 32px; border-radius:4px" :src="'http://127.0.0.1:8000/' + imgurl"/>
          </template>

          <router-link to='/profile'>
          <el-menu-item index="5-1"><i class="iconfont icon-profile"></i> 个人中心</el-menu-item>
          </router-link>

          <el-menu-item @click="logout" index="5-2"><i class="iconfont icon-exit"></i> 注销</el-menu-item>
        </el-submenu>
        <el-menu-item v-else @click="login" index="6">登陆</el-menu-item>
      </span>
    </el-menu>
  </div>
</template>

<script>
let axios = require('axios');
import SearchFrame from '@/components/search/SearchFrame'
  export default {
    components: {
      SearchFrame
    },
    data() {
      return {
        navActiveIndex: '0',
        nowuser:'',
        islogin: false,
        imgurl: '',
        hasimg: false,
        requesturl:'/api/userinfos/',
        message: [],
      }
    },
    mounted() {
      setTimeout( ()=> {
        this.nowuser = this.$store.state.nowuser
        this.islogin = this.$store.state.islogin
        console.log('nownnnnnn  ' + this.nowuser.name)
        axios.get(this.requesturl + this.nowuser.id + '/').then(res => {
          let data = res.data
          console.log(data)
          if (data.userimg_url != null) {
            this.hasimg = true
            this.imgurl = data.userimg_url
          }
          })
      }, 200)
    },
    methods: {
      msg() {
        axios.get('/api/messages/my_receive_messages/').then(res => {
          console.log(res)
          this.message = res.data.results.slice(0)
          for (var i=1; i<= res.data.count; i++) {
            this.$notify({
            title: '消息',
            message: this.message[i-1].content,
            duration: 0
          });
          }
        })
      },
      handleSelect(key, keyPath) {
        console.log(key, keyPath)
      },
      navChangeIndex(index) {
        this.navActiveIndex = index
      },
      login: function() {
        console.log('login')
        this.$router.push('/signin')
      },
      logout: function() {
        this.$store.commit('logout')
        axios.get('/api/logout/').then(res => {
          let data = res.data
          console.log(data);
        })
        this.$router.push('/signin')
        this.$cookie.delete('nowuser')
      },
      profile: function() {
        this.$router.push('/profile')
      }
    },
  }
</script>

<style>
.left-nav {
  position: relative;
  float: left;
  left: 100px;
}
.right-nav {
  position: relative;
  float: right;
  right: 120px;
}

.nav {
  width: 100%;
  height: 48px;
  position: fixed;
  top: 0px;
  z-index: 999;
  font-weight: 600;
}

.nav-search {
  position: absolute;
  z-index: 999;
  left: 740px;
  top: 4px;
}

.nav .el-menu {
  height: 48px;
}

.nav .el-submenu .el-submenu__title {
  height: 48px;
  line-height: 48px;
  font-size: 14px;
  border-bottom: none;
}
.right-nav {
  border: none;
  outline: none;
}

.right-nav .el-submenu>.el-menu {
  top: 52px;
  left: 0px;
  height: 70px;
} 

.right-nav .el-submenu>.el-menu .el-menu-item {
  min-width: 100px;
  width: 100px;
}

.nav .el-menu-item {
  height: 48px;
  line-height: 48px;
  font-size: 15px;
  text-align: left;
}

.nav .el-menu-item.is-active {
  color: #c4c5c8;
}

.nav .el-submenu .router-link-active{
  text-decoration-line: none;
}

.nav a {
  text-decoration-line: none;
}

.nav .el-menu--horizontal .el-submenu .el-submenu__icon-arrow {
  position: fixed;
  right: 118px;
  top: 21px;
}

</style>

