<template>
  <div class="app-wrap-header">
    <div class="header-left-logo">
      TOC | System
    </div>
    <div class="header-nav-menu">
      <el-menu
        :default-active="index"
        class="index-nav"
        mode="horizontal"
        active-text-color="#22A158"
        @select="changeChildrenRoute"
      >
        <el-menu-item v-for="(route, index) in childRoutes" :index="route.path" :key="index">{{ route.name }}</el-menu-item>
      </el-menu>
    </div>
    <div class="header-util-btns">
      <el-button type="primary" icon="el-icon-search" @click="changeRoute('account')">账户设置</el-button>
      <el-button type="primary" icon="el-icon-search" @click="exitLogin">退出登录</el-button>
    </div>
  </div>
</template>

<script>
import { getUserInfo, removeUserInfo } from '@/utils/auth'
import { logout } from '@/api/login/login'
export default {
  name: 'main-header',
  data () {
    return {
      index: '/',
      childRoutes: [{
        path: '/',
        name: '首页'
      }]
    }
  },
  methods: {
    changeChildrenRoute (index) {
      this.$router.push(index)
    },
    /*
    * 登出
    * */
    exitLogin () {
      logout({
        token: getUserInfo().token
      }).then(res => {
        console.log(res)
        if (res.data.code === 200) {
          removeUserInfo()
          this.$message({
            type: 'success',
            message: '登出成功！'
          })
          this.$router.push('/')
        }
      })
    },
    /*
    * 账户设置
    * */
    changeRoute (path) {
      this.$router.push({
        path: path
      })
    }
  }
}
</script>

<style lang="sass">
  @import "index"
</style>
