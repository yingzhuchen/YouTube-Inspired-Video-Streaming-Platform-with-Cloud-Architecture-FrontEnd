<script>
import fileUtils from "@/utils/fileUtils";
import videoApi from "@/api/videoApi";

export default {
  name: "PostVideo",
  mixins:[fileUtils],
  data(){
    return {
      uploadedFileName:null,
      uploadProgress:0,
      selectedFile:null,
      videoInfo:{
        url:'',
        duration:'',
        thumbnail:'',
        title:'',
        type:'',
        area:'',
        videoTagList:[],
        tags:[],
        description:''
      },
      thumbnailBase64:'',
      areas:[
        {
          value:'0',
          label:'Ghost'
        },
        {
          value:'1',
          label:'Music'
        },
        {
          value:'2',
          label:'Movie'
        },
        {
          value:'3',
          label:'Anime'
        },
        {
          value:'4',
          label:'Game'
        }
      ]
    }
  },
  methods:{

    selectVideo(){
      this.$refs.fileInput.click();
    },

    handleDrop(event){
      event.preventDefault();
      const files = event.dataTransfer.files;
      if(files.length > 0){
        this.selectedFile = files[0];
        this.uploadedFileName = this.selectedFile.name;
      }
    },

    setVideo(){
      const files = this.$refs.fileInput.files;
      if(files.length > 0){
        this.selectedFile = files[0];
        this.uploadedFileName = this.selectedFile.name;
      }
    },

    async uploadVideo(){
      if(this.selectedFile){
        const response = await this.uploadFileBySlices(this.selectedFile);
        if(response){
          const {data:data} = response;
          this.videoInfo.url = data;
          console.log(this.videoInfo.url);
          await this.getFrameAtTime(this.selectedFile);
        }
      }
    },

    async getFrameAtTime(selectedFile){
      if(!selectedFile){
        window.alert('No file selected');
        return;
      }
      const videoUrl = URL.createObjectURL(selectedFile);
      const videoElement = this.$refs.videoElement;
      videoElement.src = videoUrl;
      videoElement.addEventListener('loadedmetadata', ()=>{
        this.videoInfo.duration = videoElement.duration;
        videoElement.currentTime = 0;
        videoElement.addEventListener('timeupdate', async ()=>{
          const canvas = document.createElement('canvas');
          const context = canvas.getContext('2d');
          canvas.width = videoElement.videoWidth;
          canvas.height = videoElement.videoHeight;
          //绘制视频帧到画布上
          context.drawImage(videoElement, 0,0, canvas.width, canvas.height);
          //将画布的内容转换为图像
          this.thumbnailBase64 = canvas.toDataURL('image/jpeg');
          if(this.thumbnailBase64){
            const thumbnailPng = this.convertBase64ToPngFile(this.thumbnailBase64);
            let thumbnailUrl = await this.uploadFileBySlices(thumbnailPng);
            if(thumbnailUrl){
              console.log('thumbnailUrl', thumbnailUrl);
              this.videoInfo.thumbnail = thumbnailUrl.data;
            }
          }
        });
      });
    },

    async createTag(){
      const input = this.$refs.tagInput.value.trim();
      if(input){
        const params = {
          name:input
        }
        const response = await videoApi.addTag(params);
        const {data:tagId} = response;
        let tag = {
          tagId:tagId
        }
        this.videoInfo.videoTagList.push(tag);
        this.videoInfo.tags.push(input);
        // 清空输入框
        this.$refs.tagInput.value = ''
      }
    },

    async postVideo(){
      await videoApi.postVideo(this.videoInfo);
      window.alert('Video upload successfully');
      await this.$router.push('/')
    }

  }
}
</script>

<template>
<div class="post-video-container">
  <div class="post-video-body" @dragover.prevent @drop="handleDrop">
    <i class="el-icon-upload2"></i>
    <div v-if="uploadedFileName">{{uploadedFileName}}</div>
    <div v-else>Drag video here to upload</div>
    <input type="file" ref="fileInput" @change="setVideo"
           style="display: none" accept="video/*">
    <el-button @click="selectVideo" class="video-operation-btn" type="primary">
      Select video
    </el-button>
    <el-button @click="uploadVideo" class="video-operation-btn" type="primary">
      Upload video
    </el-button>
    <div v-if="uploadProgress > 0" class="upload-progress">
      <progress :value="uploadProgress" max="100" ></progress>
      {{uploadProgress}}%
    </div>
    <video ref="videoElement" style="display: none"></video>
  </div>

  <div class="video-form">
    <div class="video-thumbnail" v-if="videoInfo.url">
      <div class="video-thumbnail-text">
        Cover image
      </div>
      <div class="video-thumbnail-img">
        <img :src="thumbnailBase64" alt="">
      </div>
    </div>
    <div class="video-title">
      <div class="video-title-text">Title</div>
      <div class="video-title-input">
        <input type="text" v-model="videoInfo.title" maxlength="80"
               placeholder="Please enter title" class="input-val">
      </div>
    </div>
    <div class="video-type">
      <div class="video-type-text">Type</div>
      <div class="video-type-selector">
        <el-radio v-model="videoInfo.type" label="1">Self-made</el-radio>
        <el-radio v-model="videoInfo.type" label="2">Repost</el-radio>
      </div>
    </div>
    <div class="video-area">
      <div class="video-area-text">Video Area</div>
      <div class="video-area-selector">
        <el-select v-model="videoInfo.area" placeholder="Please select">
          <el-option v-for="area in areas" :key="area.value"
                     :label="area.label" :value="area.value">
          </el-option>
        </el-select>
      </div>
    </div>
    <div class="video-tag">
      <div class="video-tag-container">
        <div class="video-tag-text">Tag</div>
        <div class="video-tag-input">
          <input type="text" maxlength="80"
                 placeholder="Press Enter to create a tag"
                 class="input-val" ref="tagInput" @keyup.enter="createTag">
        </div>
      </div>
      <div v-if="videoInfo.tags.length > 0" class="video-tag-show">
        <el-tag type="info" v-for="(tag, index) in videoInfo.tags" :key="index"
                style="margin-right: 20px; margin-top: 10px; font-size: 16px">
          {{tag}}
        </el-tag>
      </div>
    </div>
    <div class="video-description">
      <div class="video-description-text">Description</div>
      <div class="video-description-input">
        <input type="text" v-model="videoInfo.description"
               maxlength="80" placeholder="Please enter description" class="input-val">
      </div>
    </div>
    <div class="submit-video">
      <el-button @click="postVideo" type="primary"
                 style="margin: 40px; width: 200px;">
        Post now
      </el-button>
    </div>
  </div>
</div>
</template>

<style scoped lang="less">

.post-video-container{
  max-width: 830px;
  margin: 0 auto;
  position: relative;
  .post-video-body{
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    margin-top: 30px;
    margin-bottom: 30px;
    position: relative;
    color: #999;
    font-size: 14px;
    border: 2px dashed #ccc;
    padding: 30px;
    .el-icon-upload2{
      font-size: 30px;
    }
    .video-operation-btn{
      margin-top: 20px;
      font-size: 16px;
      margin-left:0;
    }
    .upload-progress{
      display: flex;
      flex-direction: column;
      justify-content: center;
      margin-top: 20px;
      font-size: 16px;
      margin-left:0;
    }
  }

  .video-form{
    display: flex;
    flex-direction: column;
    justify-content: center;

    .video-thumbnail{
      display: flex;
      .video-thumbnail-text{
        width: 100px;
      }
      .video-thumbnail-img{
        img{
          width: 320px;
          height: 180px;
          border-radius: 4px;
        }
      }
    }

    .video-title{
      display: flex;
      justify-content: space-between;
      margin-top: 40px;

      .video-title-text{
        width: 100px;
      }

      .video-title-input{
        border-radius: 4px;
        padding: 0 12px;
        border: 1px solid #ccd0d7;
        min-width: 200px;
        width: 100%;
        .input-val{
          width: 100%;
          border: none;
          color: #222;
          line-height: 22px;
          height: 22px;
          outline: 0;
          border: 0;
          background-color: transparent;
          font-size: 16px;
        }
      }
    }

    .video-type{
      display: flex;
      margin-top: 40px;
      align-items: center;

      .video-type-text{
        width: 100px;
      }
      .video-type-selector{
        display: flex;
      }
    }

    .video-area{
      display: flex;
      margin-top: 40px;
      align-items: center;

      .video-area-text{
        width: 100px;
      }
      .video-area-selector{
        display: flex;
      }
    }

    .video-tag{
      display: flex;
      margin-top: 40px;
      flex-direction: column;

      .video-tag-container{
        display: flex;
        align-items: center;
        .video-tag-text{
          width: 100px;
        }
        .video-tag-input{
          border-radius: 4px;
          padding: 0 12px;
          border: 1px solid #ccd0d7;
          min-width: 200px;
          width: 100%;
          .input-val{
            width: 100%;
            border: none;
            color: #222;
            line-height: 22px;
            height: 22px;
            outline: 0;
            border: 0;
            background-color: transparent;
            font-size: 16px;
          }
        }
      }
      .video-tag-show{
      }
    }

    .video-description{
      display: flex;
      justify-content: space-between;
      margin-top: 40px;

      .video-description-text{
        width: 100px;
      }
      .video-description-input{
        border-radius: 4px;
        padding: 0 12px;
        border: 1px solid #ccd0d7;
        min-width: 200px;
        width: 100%;
        height: 60px;
        .input-val{
          width: 100%;
          border: none;
          color: #222;
          line-height: 22px;
          height: 22px;
          outline: 0;
          border: 0;
          background-color: transparent;
          font-size: 16px;
        }
      }
    }

    .submit-video{
      display: flex;
      justify-content: center;
    }

  }
}
</style>