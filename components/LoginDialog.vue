<script>
import userUtils from "@/utils/userUtils";

export default {
  name: "LoginDialog",
  mixins:[userUtils],
  data(){
    return{
      account:'',
      password:''
    }
  },
  methods:{
    async userRegisterFromDialog(){
      try{
        await this.userRegister(this.account, this.password);
        window.alert('Registration successful, please click the login button to log in');
      }catch (e){
        window.alert('Registration failed');
      }
    },

    async userLoginFromDialog(){
      try {
        await this.userLogin(this.account, this.password);
        location.reload();
      }catch (e){
        window.alert('Login failed');
      }

    }
  }
}
</script>

<template>

  <div class="login-dialog">
    <form class="login-form">
      <div class="form-item">
        <div class="form-info">
          Account
        </div>
        <input v-model="account" autocomplete="on" maxlength="32"
               placeholder="Please enter your account" type="text">
      </div>
      <div class="form-separator-line"></div>
      <div class="form-item">
        <div class="form-info">
          Password
        </div>
        <input v-model="password" autocomplete="on" maxlength="32"
               placeholder="Please enter your password" type="password">
        <div class="forget-password">Forgot password?</div>
      </div>
    </form>
    <div class="footer">
      <el-button class="user-login-form-footer-btn"
                 @click="userRegisterFromDialog" type="primary">
        Register
      </el-button>
      <el-button class="user-login-form-footer-btn"
                 style="background: #00aeec; color: #fff;"
                 @click="userLoginFromDialog" type="primary">
        Login
      </el-button>
    </div>
  </div>

</template>

<style scoped lang="less">
.login-dialog{
  display: flex;
  align-items: center;
  flex-direction: column;

  .login-form {
    width: 400px;
    height: 100px;
    border: 1px solid #e3e5e7;
    border-radius: 8px;
    font-size: 14px;
    line-height: 20px;
    margin-bottom: 20px;

    .form-item {
      display: flex;
      justify-content: flex-start;
      align-items: center;
      box-sizing: border-box;
      width: 100%;
      height: 44px;
      padding: 12px 20px;
      margin-top: 2px;

      input {
        flex: 1;
        min-width: 0;
        margin-left: 20px;
        margin-right: 10px;
      }
    }

    .form-separator-line {
      display: block;
      width: 100%;
      height: 0;
      border-bottom: 1px solid #e3e5e7;
    }
  }

  .footer{
    display: flex;
    justify-content: space-between;
    width: 400px;
    .user-login-form-footer-btn{
      box-sizing: border-box;
      width: 194px;
      height: 40px;
      cursor: pointer;
      color: #18191c;
      background: #fff;
      border: 1px solid #e3e5e7;
      border-radius: 8px;
    }
  }
}

</style>