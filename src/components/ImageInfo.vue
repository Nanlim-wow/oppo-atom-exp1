<template>
  <div class="container" :style="`background-image: url('${this.imageList[currentIndex].url}')`">

    <div :class="['info-input']">
      <div class="assessment">
        <input type="text" class="text" v-model="assessment1" />
        <input type="text" class="text" v-model="assessment2" />
        <input type="text" class="text" v-model="assessment3" />
        <input type="text" class="text" v-model="assessment4" />
        <input type="text" class="text" v-model="assessment5" />
      </div>
      <div class="picknum">
        <input type="text" class="text" v-model="assessment6"/>
      </div>
    </div>


    <!--下一张-->
    <div class="nextImage" @click="nextImage">
      <a :class="['button',{'hidden': this.isLast }]">下一张</a>
    </div>

    <!--提交-->
    <div class="nextImage" @click.stop="submitResult">
      <a :class="['button',{'hidden': !this.isLast}]">submit</a>
    </div>

    <!--结束框-->
    <div :class="['ending',{'hidden': this.hideEnd}]">
      谢谢您的参与！<br>
      Thank you!!
    </div>

    <b-loading :is-full-page="true" :active.sync="isLoading" :can-cancel="false"></b-loading>
  </div>
</template>
<script>
import { IMAGE_LIST } from '../data/imageList'
export default {
  name: 'HelloWorld',
  data () {
    return {
      assessment1: '',
      assessment2: '',
      assessment3: '',
      assessment4: '',
      assessment5: '',
      assessment6: '',
      lasthideHint: false,
      nexthideHint: true,
      userComment: '',
      List: IMAGE_LIST,
      currentIndex: 1,
      isFirst: true,
      isLast: false,
      hideEnd: true,
      userCommentList: [],
      imageList:IMAGE_LIST,
      randomList:[],
      response: '',
      isLoading: false,
      submitted: false,
      raceimage:[],
    }
  },
  mounted () {
    // 初始化数据
    this.currentIndex = 0
    this.isFirst = true
    this.isLast = false
    // this.hideEnd = true

    console.log(this.imageList.length) 
  },
  methods: {

    nextImage () {
      let userComments = this.userCommentList
      if (userComments.length < this.imageList.length){
        this.assessment1 = this.assessment1.concat(",");
        this.assessment2 = this.assessment2.concat(",");
        this.assessment3 = this.assessment3.concat(",");
        this.assessment4 = this.assessment4.concat(",");
        this.assessment5 = this.assessment5.concat(",");
        userComments.push(this.assessment1.concat(this.assessment2.concat(this.assessment3.concat(this.assessment4.concat(this.assessment5.concat(this.assessment6))))))
      }
      
      this.userCommentList = userComments
      console.log(typeof this.assessment1.concat(this.assessment2.concat(this.assessment3.concat(this.assessment4.concat(this.assessment5.concat(this.assessment6)))))) 
      console.log(typeof this.userCommentList)  
      console.log(typeof this.$route.query.name)  
      let length = this.imageList.length
      if (this.currentIndex < length - 1) {
        this.currentIndex++
      }

      this.isLast = this.currentIndex === length - 1
      this.isFirst = this.currentIndex === 0
      this.assessment1 = ''
      this.assessment2 = ''
      this.assessment3 = ''
      this.assessment4 = ''
      this.assessment5 = ''
      this.assessment6 = ''
      console.log(this.userCommentList)      
      //this.hideHint = true
    },

    submitResult () {
      if(this.isLoading){
        return;
      }

      //上传实验数据
      this.isLoading = true;
      let userComments = this.userCommentList
      let self = this;
      if (userComments.length < this.imageList.length){
        this.assessment1 = this.assessment1.concat(",");
        this.assessment2 = this.assessment2.concat(",");
        this.assessment3 = this.assessment3.concat(",");
        this.assessment4 = this.assessment4.concat(",");
        this.assessment5 = this.assessment5.concat(",");
        userComments.push(this.assessment1.concat(this.assessment2.concat(this.assessment3.concat(this.assessment4.concat(this.assessment5.concat(this.assessment6))))))
      }
      this.userCommentList = userComments
      // this.hideEnd = false

      let picList = this.imageList
      for (let i = 0; i < picList.length; i++) {
        picList[i].response = userComments[i]
        picList[i].name = this.$route.query.name
        picList[i].age = this.$route.query.age
      }
    
      console.log(picList)
      

      
      
      let promise = axios.post('http://120.79.147.66:8081/survey/submit',{
              data: picList
          });

      Promise.all([promise]).then((res) => {
          self.isLoading = false;
          self.hideEnd = false;
          self.submitted = true;
          setTimeout(() => {
            this.isSubmitting = false
            /*this.$router.push({
              name: 'UserInfo'
            })*/
          },3000)
      }).catch((e) => {
          self.isLoading =false;
          self.$toast.open({
              message: e || '服务器错误',
              type: 'is-danger'
          })
      })
    }
  }
}
</script>
<style lang="less" scoped>
.container {
  width: 100vw;
  height: 100vh;
  max-width: 100vw;
  max-height: 100vh;
  // top: 0;
  // left: 0;
  background-color: rgb(128,128,128);
  background-image: url("https://wx4.sinaimg.cn/mw690/007xZLPUly1fyzc5f36mbj30k00dcgmy.jpg");
  
  background-position: center;
  background-repeat: no-repeat;
  background-size: 100% 100%;

  .info-input {
    position: fixed;
    left: 25%;
    bottom: 2%;
    height:15%;
    width:50%;
    &.hidden {
      display: none;
    }
    .assessment {
      font-weight: bold;
      height:50%;
      width:100%;
      .text{
        background-color: rgb(128,132,130);
        height: 50%;
        width:16%;
      }
    }
    .picknum {
      height: 50%;
      font-weight: bold;
      width:100%;
      .text{
        position: absolute;
        height: 25%;
        width:16%;
        right:0%;
        background-color: rgb(128,132,130);
        position: absolute;
      }
    }

  }
  
  .background-image {
    height: 100vh;
    width: 50%;
  }

  .nextImage {
    position: absolute;
    right: 5%;
    bottom: 5%;  
  }
  .button {
  background-color: rgba(128,128,128,0);
  padding: 10px;
  opacity: 1;
  visibility: visible;
  border-color: rgb(0,0,0);
  font-weight: bold;
  color: rgb(0,0,0);
  /*transition: all 0.8s linear;*/
  &.hidden {
    display: none;
    }
  }


  .ending {
  position: absolute;
  height: 100vh;
  width: 100vw;
  background-color: rgba(255,255,255,0.8);
  z-index: 99;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.5rem;
  font-weight: bold;
  color: brown;
  &.hidden {
    display: none;
    }
  }
}
</style>