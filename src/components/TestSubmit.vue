<template>
  <div class="container":style="`background-image: url('http://10.12.15.27/Pictures/CEL/20151210-Christmas%20Party.jpeg')`">
    <b-field>
    <a class="button is-primary" @click="submitInfoList">测试提交全部评价</a>
    </b-field>
    <b-loading :is-full-page="true" :active.sync="isLoading" :can-cancel="false"></b-loading>
  </div>
</template>
<script>
import { IMAGE_LIST } from '../data/imageList'

export default {
  data () {
    return {
      length: 0,
      isLoading: false,
    }
  },
  mounted () {
  },
  methods: {
    //提交数据
    submitInfoList(){
        let self = this;
        let length = IMAGE_LIST.length;
        let commitList = new Array(length);
        commitList.fill({
            age: "23",
            name: "1231",
            pic: "BK_F1.jpg",
            response: 4,
            url: "http://pv707742q.bkt.clouddn.com/BK_F1.jpg",
        });
        //分割实验数据
        let perListLength = length / 3;
        let picList1 = commitList.slice(0,perListLength);
        let picList2 = commitList.slice(perListLength,perListLength*2);
        let picList3 = commitList.slice(perListLength*2);

        let promise1 = axios.post('http://120.79.147.66:8081/survey/submit',{
                data: picList1
            });
        let promise2 = axios.post('http://120.79.147.66:8081/survey/submit',{
                data: picList2
            });
        let promise3 = axios.post('http://120.79.147.66:8081/survey/submit',{
                data: picList3
            });
   
        Promise.all([promise1,promise2,promise3]).then((res) => {
            this.isLoading = false;
            this.$toast.open({
                message: '提交成功'
            })
        }).catch((e) => {
            this.isLoading =false;
            this.$toast.open({
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
  background-color: rgb(128,132,130);
  background-image: url("http://10.12.15.27/Pictures/CEL/20151210-Christmas%20Party.jpeg");
  background-size: contain;
  background-position: center;
  background-repeat: no-repeat;
}
</style>
