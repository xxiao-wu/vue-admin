<template>
  <div style="width: 100%;height: 100vh;background-color: cornflowerblue;overflow: hidden">
    <div style="width: 400px;margin: 150px auto">
      <div style="color: aliceblue;font-size: 30px;text-align: center;padding: 30px 0">欢迎注册</div>

      <el-form ref="registerForm" :model="registerForm" :rules="registerRules" size="normal">
        <el-form-item prop="username"><el-input v-model="registerForm.username" :prefix-icon="User" placeholder="请输入用户名"></el-input></el-form-item>
        <el-form-item prop="password">
          <el-input v-model="registerForm.password" :prefix-icon="Lock" show-password placeholder="请输入密码"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="registerForm.checkPass" :prefix-icon="Lock" show-password placeholder="请再次输入密码"></el-input>
        </el-form-item>
        <el-form-item ><el-button style="width: 100%" type="primary" @click="register">注册</el-button></el-form-item>
      </el-form>
    </div>

  </div>
</template>

<script >
import { User, Lock} from '@element-plus/icons'
import request from "../utils/request";
export default {
  name: "Register",
  data(){
    return {
        User,
        Lock,
        registerForm: {},
        registerRules: {
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
          checkPass:[
            {
              required: true,
              message: '请再次输入密码',
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
    register(){
      if (this.registerForm.password !== this.registerForm.checkPass){
        this.$message({
          type: 'error',
          message: '两次输入密码不一致'
        })
        return
      }
      this.$refs['registerForm'].validate((valid) => {
        if (valid) {
          request.post("/user/register",this.registerForm).then(res =>{
            console.log(res)
            if(res.status=="0"){
              this.$message({
                type: 'success',
                message: '注册成功'
              })
              this.$router.push('/login')
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