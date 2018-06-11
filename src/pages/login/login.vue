<template>
  <div id="login" v-loading="loading">
    <div class="shadow">
      <ul class="header">
        <li class="logo"></li>
        <li class="line"></li>
        <li class="lable">TOC System</li>
      </ul>
    </div>
    <div class="login-content">
      <div style="margin-top:35px; margin-bottom: 20px; color: green; text-align: center; font-size: 20px; line-height: 1.5;">
        <p>TOC</p>
        <p>System</p>
      </div>
      <div v-if="isActive" key="1">
        <el-form :model="formData" :rules="rules1" ref="formData" class="demo-ruleForm">
          <el-form-item prop="phoneNumber" :rules="isActive === true ? rules1.phoneNumber:[]">
            <el-input type="" v-model="formData.accountCode" auto-complete="off" placeholder="账号"></el-input>
          </el-form-item>
          <el-form-item prop="password" :rules="isActive === true ? rules1.password:[]">
            <el-input type="password" v-model="formData.accountPassword" auto-complete="off" placeholder="密码"></el-input>
          </el-form-item>
          <el-form-item prop="verifyCode" :rules="isActive === true ? rules1.password:[]">
            <el-input style="width: 60%;" type="text" v-model="formData.verifyCode" auto-complete="off" placeholder="验证码"></el-input>
            <img @click="changeVerifyImg" style="float: right;" :src="verifyImgSrc" alt="">
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitPassWord('formData')" style="width:100%">登录</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>
<script>
import { accountLogin, getVerifyCode } from '@/api/login/login'
import { setUserInfo } from '@/utils/auth'
const uuid = () => {
  var s = []
  var hexDigits = '0123456789abcdef'
  for (var i = 0; i < 36; i++) {
    s[i] = hexDigits.substr(Math.floor(Math.random() * 0x10), 1)
  }
  s[14] = '4'
  s[19] = hexDigits.substr((s[19] & 0x3) | 0x8, 1)
  s[8] = s[13] = s[18] = s[23] = '-'
  var uuid = s.join('')
  return uuid
}
export default {
  name: 'login',
  data () {
    return {
      isActive: true,
      isActiveCode: false,
      showDialog: false,
      loading: false,
      count: '发送验证码',
      timeCode: 60,
      timer: null,
      formData: {
        accountCode: '',
        accountPassword: '',
        verifyCode: ''
      },
      rules1: {
        // phoneNumber: [
        //   { required: true, message: '请输入手机号码', trigger: 'change' },
        //   { pattern: /^[1][3,4,5,6,7,8,9][0-9]{9}$/, message: '请输入正确手机号码格式！', trigger: 'change' }
        // ],
        password: [
          { required: false, message: '请输入密码', trigger: 'change' }
        ]
      },
      verifyImgSrc: '',
      pageToken: uuid()
    }
  },
  created () {
    this.getVerifyCode()
  },
  methods: {
    /*
    * 获取验证码
    * */
    getVerifyCode () {
      getVerifyCode({
        pageToken: this.pageToken
      }).then(res => {
        console.log(res)
        if (res.data.code === 200) {
          this.verifyImgSrc = `data:image/jpeg;base64,${res.data.data}`
        }
      })
    },
    submitPassWord (formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          let params = Object.assign({}, this.formData, {
            pageToken: this.pageToken
          })
          this.loading = true
          accountLogin(params).then((res) => {
            this.loading = false
            if (res.data.code === 200) {
              // 存储用户信息
              setUserInfo(JSON.stringify(res.data.data))
              this.$message({
                message: '登录成功',
                type: 'success'
              })
              setTimeout(() => {
                if (parseInt(res.data.data.accountType, 10) === 1) {
                  this.$router.push({
                    path: '/management'
                  })
                } else {
                  this.$router.push({
                    path: '/front'
                  })
                }
              }, 300)
            }
            if (res.data.code === 10101) {
              this.$message({
                message: res.data.data,
                type: 'error'
              })
              // 重新加载数据
              this.formData.verifyCode = ''
              this.getVerifyCode()
            }
          })
        } else {
          this.loading = false
          console.log('error submit!!')
          return false
        }
      })
    },
    /*
    * 更改验证码图片
    * */
    changeVerifyImg () {
      this.getVerifyCode()
    }
  }
}
</script>
<style scoped lang="scss">
  #login{
    margin: 0 auto;
    .shadow{
      box-shadow: 0 5px 5px 0 rgba(0,0,0,0.05);
      .header{
        width: 1200px;
        margin: 0 auto;
        height:80px;
        background: #FFFFFF;
        padding:13px 0;
        li{
          float:left;
        }
        .logo{
          width:164px;
          height:54px;
          /*background:url(../../../assets/person/securitySettings/logo2x.png) no-repeat;*/
          background-size:contain;
        }
        .line{
          height:40px;
          border-left: 1px solid #C0C0C0;
          margin:8px 20px;
        }
        .lable{
          height:54px;
          line-height:54px;
          font-size: 18px;
          color: #333333;
          font-family: PingFangSC-Semibold;
        }
        .back{
          float:right;
          font-family: MicrosoftYaHei;
          font-size: 14px;
          color: #333333;
          text-align: right;
          height:54px;
          line-height:54px;
        }
      }
    }
    .login-content{
      width: 350px;
      margin: 0 auto;
      margin-top: 100px;
      height: 700px;
    }
    .active {
      border-bottom: 4px solid #25B369;
      font-size: 22px !important;
      color: #333333 !important;
      padding: 5px 0;
    }
    .login-titel {
      font-size: 19px;
      color: #999999;
      height: 28px;
      line-height: 28px;
      margin-right: 42px;
    }
    .send-code{
      background: #DDDDDD;
      padding:10px 33px;
      border: 1px solid #CCCCCC;
      border-radius: 2px;
    }
    .login-password{
      font-size: 14px;
      color: #333333;
      margin-right: 6px;
    }
    .login-register{
      font-size: 14px;
      color: #FF7515;
    }
  }
</style>
