<template>

  <el-menu
      style="width: 200px;min-height:calc(100vh - 50px)"

      class="el-menu-vertical-demo"
      router
  >
    <el-sub-menu index="1" >
      <template #title>
        <el-icon><location /></el-icon>
        <span>系统管理</span>
      </template>

        <el-menu-item index="user" v-if="this.user.role==='admin'">用户信息管理</el-menu-item>
        <el-menu-item index="person">个人信息</el-menu-item>
        <el-menu-item index="book">书籍</el-menu-item>

      <el-sub-menu index="1-4">
        <template #title><span>新闻</span></template>
        <el-menu-item index="news">item one</el-menu-item>
      </el-sub-menu>
    </el-sub-menu>

    <el-sub-menu index="2" >
      <template #title>
        <el-icon><icon-menu /></el-icon>
        <span>系统管理</span>
      </template>
        <el-menu-item index="2-1" >用户信息管理</el-menu-item>
        <el-menu-item index="2-2">item two</el-menu-item>
        <el-menu-item index="2-3">item three</el-menu-item>
    </el-sub-menu>

    <el-sub-menu index="3" >
      <template #title>
        <el-icon><document /></el-icon>
        <span>系统管理</span>
      </template>
        <el-menu-item index="3-1">用户信息管理</el-menu-item>
        <el-menu-item index="3-2">item two</el-menu-item>
        <el-menu-item index="3-3">item three</el-menu-item>
    </el-sub-menu>

    <el-sub-menu index="4">
      <template #title>
        <el-icon><setting /></el-icon>
        <span>系统管理</span>
      </template>
        <el-menu-item index="4-1">用户信息管理</el-menu-item>
        <el-menu-item index="4-2">item two</el-menu-item>
        <el-menu-item index="4-3">item three</el-menu-item>
    </el-sub-menu>
  </el-menu>
</template>
<script>
import {
  Location,
  Document,
  Menu as IconMenu,
  Setting,
} from '@element-plus/icons'
import request from "../utils/request";

export default {
  name:"SideMenu",
  components:{
    Location,
    Document,
    Setting,
    IconMenu,

  },
  data(){
    return {
      user:{},
      path:this.$route.path
    }
  },
  created() {
      let userStr =sessionStorage.getItem("user")||"{}"
      this.user=JSON.parse(userStr)
      request.get("/user/selectOne",{params:{
          id:this.user.id
        }
      }).then(res=>{
        if (res.status=="0"){
          this.user=res.data
        }
      })
  }
}
</script>

<style scoped>

</style>
