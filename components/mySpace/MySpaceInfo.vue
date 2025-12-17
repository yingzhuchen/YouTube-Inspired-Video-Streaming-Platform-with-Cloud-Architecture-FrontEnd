<script>
import userUtils from "@/utils/userUtils";
import userApi from "@/api/userApi";
import dateUtils from "@/utils/dateUtils";

export default {
  name: "MySpaceInfo",
  mixins:[userUtils, dateUtils],
  data(){
    return{
      userInfo:{
        nick:'nick',
        userId:0,
        sign:'sign',
        gender:0,
        birth:'1999-01-01'
      }
    }
  },

  methods:{

    async getUserBasicInfo(){
      let response = await userApi.getUserBasicInfo();
      if(response.data){
        this.userInfo = response.data.userInfo;
      }
    },

    async updateUserInfo(){
      this.userInfo.birth = this.formatDate(this.userInfo.birth, 'yyyy-MM-dd');
      await userApi.updateUserInfo(this.userInfo);
      window.alert('Update successfully');
      location.reload();
    }
  },

  mounted() {
    if(this.isUserLoggedIn){
      this.getUserBasicInfo();
    }
  }
}
</script>

<template>
<div class="my-space-info">
  <div class="my-space-info-title">
    My info
  </div>
  <div class="my-space-info-item">
    <div class="my-space-info-item-title">
      Nickname：
    </div>
    <div class="my-space-info-item-body">
      <input type="text" autocomplete="off"
             placeholder="Your nickname" maxlength="16"
             style="height:30px; width: 225px;
             outline: none; font-size: 18px;"
             v-model="userInfo.nick">
    </div>
  </div>

  <div class="my-space-info-item">
    <div class="my-space-info-item-title">
      User ID：
    </div>
    <div class="my-space-info-item-body">
      <span style="color: rgb(128,128,128); font-size: 18px;">
        {{userInfo.userId}}
      </span>
    </div>
  </div>

  <div class="my-space-info-item">
    <div class="my-space-info-item-title">
      My intro：
    </div>
    <div class="my-space-info-item-body">
      <textarea autocomplete="off"
                placeholder="Set your intro- ( ゜- ゜)つロ" rows="2"
                style="min-height: 32px;
                width: 618px; height: 88px;
                resize: none; font-size: 18px; outline: none;"
                v-model="userInfo.sign">
      </textarea>
    </div>
  </div>

  <div class="my-space-info-item">
    <div class="my-space-info-item-title">
      My gender：
    </div>
    <div class="my-space-info-item-body">
      <div>
        <el-radio-group v-model="userInfo.gender">
          <el-radio :label="'0'">Male</el-radio>
          <el-radio :label="'1'">Female</el-radio>
          <el-radio :label="'2'">Decide not to tell</el-radio>
        </el-radio-group>
      </div>
    </div>
  </div>

  <div class="my-space-info-item">
    <div class="my-space-info-item-title">
      Birthday：
    </div>
    <div class="my-space-info-item-body">
      <el-date-picker
          v-model="userInfo.birth"
          type="date"
          placeholder="Pick a date">
      </el-date-picker>
    </div>
  </div>

  <div class="border-line"></div>
  <div class="submit-btn">
    <el-button type="primary" style="width: 140px;"
    @click="updateUserInfo">Save</el-button>
  </div>

</div>
</template>

<style scoped lang="less">

.my-space-info{
  margin-top: 40px;
  margin-left: 20%;
  margin-right: 20%;

  .my-space-info-title{
    font-size: 18px;
    font-weight: bold;
    color: deepskyblue;
    padding: 10px;
    border-bottom: darkgray solid 1px;
  }

  .my-space-info-item{
    display: flex;
    align-items: center;
    padding: 20px;

    .my-space-info-item-title{
      font-size: 18px;
      margin-right: 20px;
    }

  }

  .border-line{
    border-bottom: darkgray solid 1px;
    margin-bottom: 40px;
  }

  .submit-btn{
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 40px;
  }
}


</style>