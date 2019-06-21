<template>
  <div class="site-wrapper site-page--login">
    <div class="site-content__wrapper">
      <div class="site-content">
        <div class="logoImg"></div>
        <el-tabs v-model="activeName">
          <el-tab-pane label="二维码登录" name="first">
            <img src="../../assets/img/login_code.png" alt="" class="codeImg">
            <p class="codeTips">打开领旋智慧APP，点击扫一扫登录</p>
          </el-tab-pane>
          <el-tab-pane label="密码登录" name="second">
            <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" class="dataForm">
              <el-form-item prop="userName">
                <el-input v-model="dataForm.userName" placeholder="帐号" clearable></el-input>
              </el-form-item>
              <el-form-item prop="password">
                <el-input v-model="dataForm.password" type="password" placeholder="密码" clearable></el-input>
              </el-form-item>
              <el-form-item prop="captcha">
                <el-row :gutter="10">
                  <el-col :span="14">
                    <el-input v-model="dataForm.captcha" placeholder="验证码" clearable>
                    </el-input>
                  </el-col>
                  <el-col :span="10" class="login-captcha">
                    <img :src="captchaPath" @click="getCaptcha()" alt="">
                  </el-col>
                </el-row>
              </el-form-item>
              <el-form-item prop="remember">
                <div class="login-remember">
                  <el-checkbox v-model="dataForm.remember">记住用户名</el-checkbox>
                  <span class="forget" @click="forgetPas">忘记密码？</span>
                </div>
              </el-form-item>
              <el-form-item>
                <el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
        </el-tabs>
      </div>
      <div class="site-tips">
        <ul>
          <li class="side1" @click="sideHandle(1)" :class="{'open1': sideModel1}">
            <p class="phone" ref="phone"><span>400电话：</span><a href="tel:400-655-6508">400-655-6508</a></p>
          </li>
          <li class="side2" @click="sideHandle(2)" :class="{'open2': sideModel2}">
            <div class="downCode" ref="downCode">
              <p class="title">扫码下载领旋APP</p>
              <div class="code"></div>
            </div>
          </li>
          <li class="side3" @click="sideHandle(3)"><a href="http://www.lead-win.cn/" target="_blank"></a></li>
        </ul>
      </div>
      <div class="site-copy">
        <p>版权所有©北京领旋智慧农业科技有限公司  京ICP备18033516号</p>
      </div>
    </div>
  </div>
</template>

<script>
  import { getUUID } from '@/utils'
  export default {
    data () {
      return {
        activeName: 'first',
        dataForm: {
          userName: '',
          password: ''
        },
        dataRule: {
          userName: [
            { required: true, message: '帐号不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          captcha: [
            { required: true, message: '验证码不能为空', trigger: 'blur' }
          ]
        },
        captchaPath: '',
        sideModel1: false,
        sideModel2: false
      }
    },
    created () {
      this.getCaptcha()
      if (window.sessionStorage.getItem('userName')) {
        this.dataForm.userName = window.sessionStorage.getItem('userName')
      }
    },
    methods: {
      // 提交表单
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$axios.adornUrl('/sys/login'),
              method: 'post',
              data: this.$http.adornData({
                'username': this.dataForm.userName,
                'password': this.dataForm.password,
                'uuid': this.dataForm.uuid,
                'captcha': this.dataForm.captcha
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                if (this.dataForm.remember) {
                  window.sessionStorage.setItem('userName', this.dataForm.userName)
                }
                this.$cookie.set('token', data.token)
                this.$router.replace({ name: 'home' })
              } else {
                this.getCaptcha()
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      getCaptcha () {
        this.dataForm.uuid = getUUID()
        this.captchaPath = this.$axios.adornUrl(`/captcha.jpg?uuid=${this.dataForm.uuid}`)
      },
      forgetPas () {
        this.activeName = 'first'
      },
      sideHandle (flag) {
        if (flag === 1) {
          this.sideModel1 = !this.sideModel1
          this.sideModel2 = false
        } else if (flag === 2) {
          this.sideModel1 = false
          this.sideModel2 = !this.sideModel2
        } else if (flag === 3) {
          this.sideModel1 = false
          this.sideModel2 = false
        }
      }
    }
  }
</script>
<style lang="scss">
  .dataForm {
    margin-top: 15px;
    .el-form-item {
      margin-bottom: 18px;
      &:last-child {
        margin-bottom: 0;
      }
      &:nth-child(3), &:nth-child(4) {
        margin-bottom: 10px;
      }
      .login-captcha {
        overflow: hidden;
        img {
          width: 100%;
          height: 33px;
          cursor: pointer;
        }
      }
      .login-remember {
        overflow: hidden;
        .forget {
          cursor: pointer;
          float: right;
          color: #009c8e;
          font-size: 12px;
          line-height: 28px;
        }
        .el-checkbox {
          float: left;
          height: 28px;
          line-height: 28px;
          margin-right: 0;
          .el-checkbox__inner {
            width: 12px;
            height: 12px;
            &:after {
              top: 0;
              left: 3px;
            }
          }
          .el-checkbox__label {
            padding-left: 5px;
            line-height: 15px;
            font-size: 12px;
          }
        }
      }
      .el-button {
        padding: 0;
        height: 44px;
        line-height: 44px;
        width: 100%;
        font-size: 16px;
        letter-spacing: 8px;
      }
    }
  }
</style>

<style lang="scss" scoped>
.site-page--login {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  .site-content__wrapper {
    margin: 0;
    padding: 0;
    background: transparent;
    height: 100%;
    width: 100%;
    background-image: url(~@/assets/img/login_bg.jpg);
    background-size: cover;
    .site-content {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      z-index: 1;
      width: 420px;
      height: 470px;
      margin: auto;
      background: #fff;
      text-align: center;
      padding: 40px 50px;
      border-radius: 3px;
      .logoImg {
        width: 220px;
        height: 70px;
        margin: 0 auto;
        background-size: cover;
        background-image: url(~@/assets/img/login_logo.png);
      }
      /deep/ .el-tabs {
        .el-tabs__header {
          position: static;
          padding: 0;
          box-shadow: none;
          margin-top: 10px;
          margin-bottom: 0;
          .el-tabs__active-bar {
            width: 160px !important;
          }
          .el-tabs__item {
            width: 160px;
            padding: 0;
            border-bottom: 1px solid #E4EBEB;
            font-size: 20px;
            height: 57px;
            line-height: 57px;
          }
        }
        .el-tabs__content {
          .codeImg {
            width: 143px;
            margin-top: 40px;
          }
          .codeTips {
            text-align: center;
            font-size: 12px;
            color: #809090;
            margin-top: 30px;
          }
        }
      }
    }
    .site-tips {
      position: fixed;
      bottom: 18.36547%;
      right: 0;
      width: 36px;
      height: 108px;
      background-color: #3B4343;
      z-index: 1000;
      ul {
        list-style: none;
        margin: 0;
        padding: 0;
        li {
          width: 100%;
          height: 36px;
          background-size: 20px;
          background-position: center;
          position: relative;
          background-repeat: no-repeat;
          cursor: pointer;
          &:hover {
            background-color: #e88e3e;
          }
          p {
            margin: 0;
          }
        }
        .side1 {
          background-image: url(~@/assets/img/aside_icon1.png);
          .phone {
            width: 0;
            height: 36px;
            line-height: 36px;
            font-size: 16px;
            position: absolute;
            top: 0;
            bottom: 0;
            right: 40px;
            background-color: #fff;
            overflow: hidden;
            transition: all 0.3s;
            box-shadow: 0px 3px 15px 0px rgba(128,144,144,0.16);
            span {
              color: #809090;
              float: left;
              box-sizing: border-box;
              padding-left: 11px;
            }
            a {
              color: #009c8e;
              float: left;
            }
          }
        }
        .open1 {
          .phone {
            width: 198px;
          }
        }
        .side2 {
          background-image: url(~@/assets/img/aside_icon2.png);
          .downCode {
            width: 0;
            padding: 0;
            height: 200px;
            position: absolute;
            top: 50%;
            bottom: 0;
            right: 40px;
            background-color: #fff;
            overflow: hidden;
            transform: translateY(-50%);
            transition: all 0.3s;
            box-shadow: 0px 3px 15px 0px rgba(128,144,144,0.16);
            .title {
              text-align: center;
              font-size: 14px;
              color: #3B4343;
              padding-bottom: 10px;
            }
            .code {
              background-image: url(~@/assets/img/download_code.png);
              background-size: cover;
              width: 138px;
              height: 138px;
            }
          }
        }
        .open2 {
          .downCode {
            width: 170px;
            padding: 16px;
          }
        }
        .side3 {
          background-image: url(~@/assets/img/aside_icon3.png);
          a {
            display: inline-block;
            width: 100%;
            height: 100%;
          }
        }
      }
    }
    .site-copy {
      position: absolute;
      left: 0;
      right: 0;
      bottom: 20px;
      color: #fff;
      font-size: 12px;
      text-align: center;
    }
  }
}
</style>
