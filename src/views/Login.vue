<template>
  <div style="width: 100%;height: 100vh;background-color: cornflowerblue;overflow: hidden">
    <div style="width: 400px;margin: 150px auto">
      <div style="color: aliceblue;font-size: 30px;text-align: center;padding: 30px 0">欢迎登陆</div>

      <el-form ref="loginForm" :model="loginForm" :rules="loginRules" size="normal">
        <el-form-item prop="username"><el-input v-model="loginForm.username" :prefix-icon="User"></el-input></el-form-item>
        <el-form-item prop="password"><el-input v-model="loginForm.password" :prefix-icon="Lock" show-password></el-input></el-form-item>
        <el-form-item ><el-button style="width: 100%" type="primary" @click="login">登录</el-button></el-form-item>
      </el-form>
    </div>

  </div>
</template>

<script >
import { User, Lock} from '@element-plus/icons'
import request from "../utils/request";
export default {
  name: "Login",
  data(){
    return {
        User,
        Lock,
        loginForm: {},
        loginRules: {
          username:[
            {
              required: true,
              message: '请输入用户名',
              trigger: 'blur',
            },
            {
              min: 3,
              max: 20,
              message: '长度应为为3~20',
              trigger: 'change',
            },

          ],
          password:[
            {
              required: true,
              message: '请输入密码',
              trigger: 'blur',
            },
            {
              min: 6,
              max: 18,
              message: '长度应为为6~18',
              trigger: 'change',
            }
          ],
        }
    }
  },

  methods:{
    login(){
      this.$refs['loginForm'].validate((valid) => {
        if (valid) {
          request.post("/user/login",this.loginForm).then(res =>{
            console.log(res)
            if(res.status=="0"){
              this.$message({
                type: 'success',
                message: '登录成功'
              })
              sessionStorage.setItem("user",JSON.stringify(res.data))
              this.$router.push('/')
            }else {
              this.$message({
                type: 'error',
                message: res.message
              })

            }
          })
        }
      })

    }
  }
}
</script>

<style scoped>

</style>