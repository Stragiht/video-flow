<template>
  <div class="hello">
   <div class="video" v-for="video in playVideoArr">
     <video :ref="'video'+video.id" controls="controls" autoplay></video>
     <button @click="stop(video.id)">暂停{{video.id}}</button>
     <button @click="play(video.id)">播放</button>
   </div>
    <input type="text" placeholder="请输入轮播时间" v-model="time">
    <input type="text" placeholder="请输入轮播数量" v-model="number">
    <button @click="playRound()">轮播</button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
	      data () {
		      return {
			      videoData: [
			      	{'url':'http://hls.open.ys7.com/openlive/58d4bb7d6c8148bf80372d6bd5cf929f.m3u8',id:1234},
                    {'url':'http://hls.open.ys7.com/openlive/348e335bb90d474d89492a0c2a874663.m3u8',id:12345},
                    {'url':'http://hls.open.ys7.com/openlive/6b20d8c1e7664dec86bb1a6625500efb.m3u8',id:12346},
                    {'url':'http://hls.open.ys7.com/openlive/5cb54cee0a454e91a4355eabcb48c82f.m3u8',id:12347},
                    {'url':'http://hls.open.ys7.com/openlive/092f30bd10bd43e4ba0d214911281dfd.m3u8',id:12348},
                    {'url':'http://hls.open.ys7.com/openlive/6b20d8c1e7664dec86bb1a6625500efb.m3u8',id:123410},
                    {'url':'http://hls.open.ys7.com/openlive/5cb54cee0a454e91a4355eabcb48c82f.m3u8',id:123411},
                    {'url':'http://hls.open.ys7.com/openlive/092f30bd10bd43e4ba0d214911281dfd.m3u8',id:123412},
                    {'url':'http://hls.open.ys7.com/openlive/092f30bd10bd43e4ba0d214911281dfd.m3u8',id:123413},
                    {'url':'http://hls.open.ys7.com/openlive/092f30bd10bd43e4ba0d214911281dfd.m3u8',id:123414},
                ],
			      playVideoArr:[],
                  number:'',
                  time:'',
                  timer:'',
                videoObj:{}
    }
  },
  mounted(){
	  this.playVideoArr = Object.assign([],this.videoData)
	  this.$nextTick(()=>{
          this.videoInit()
      })
  },
  methods:{
      videoInit(){
	      if(Hls.isSupported()) {
		      this.playVideoArr.map((item)=>{
			      this.videoObj['video'+item.id] = new Hls();
			      let video =this.$refs['video'+item.id][0];
			      this.videoObj['video'+item.id].loadSource(item.url);
			      this.videoObj['video'+item.id].attachMedia(video);
			      this.videoObj['video'+item.id].on(Hls.Events.MANIFEST_PARSED,function() {
				      video.play();
			      });
		      });
	      }
      },
	  stop(id){
		  this.videoObj['video'+id].stopLoad()
      },
	  play(id){
		  this.videoObj['video'+id].startLoad()
		  this.$refs['video'+id][0].play()
	  },
      stopLoad(){
	  	for (let i in this.videoObj){
		    this.videoObj[i].detachMedia()
        }
      },
	  playRound(){
	  	this.copyData = Object.assign([],this.videoData);
	  	//当输入数量大于已有数量时 提示不能选择大于已选视频数量
	  	if(this.number > this.copyData.length){
	  		alert('视频数量不够')
            return
        }
        //两者相等时 直接初始化 播放器
        if(this.number === this.copyData.length){
	        this.videoInit()

        }
        // 当输入数量小于当前选择视频数量时处理
        if(this.number < this.copyData.length ){
	         // 0 代表小于等于 1 代表大于
        	this.playVideoArr = this.copyData.splice(1,this.number); //播放视频
        	this.remainArr = this.copyData // 剩余视频
            // 分为三种情况处理
            // 1 剩余的视频播放数量跟选择的播放数量相等时 直接将播放数量放在待播放视频一起拼接
            if(this.playVideoArr.length === this.remainArr.length){
	            this.flag = 0  // 0 代表小于等于 1 代表大于
	            //this.copyData = this.remainArr.concat(this.playVideoArr)
            }
            // 2待播放视频数量小于已选择播放数量 ，下次播放时重新计算 需要从播放数据里面去取 其实跟1一样
            if(this.remainArr.length < this.playVideoArr.length ){
	            this.flag = 1

            }
            // 3 如果剩余待播放数量大于正在播放数量 则下次轮播时拿到的数据为剩下来的数据
	        if(this.remainArr.length > this.playVideoArr.length ){
		        this.flag = 1

	        }
	        this.lunboInit()
        }
      },
      lunboInit(){
	  	if(this.timer){
	  		clearTimeout(this.timer)
        }
        //this.stopLoad()
	     this.timer = setTimeout(()=>{
		   /*  if(this.videoObj){
			     for (let i in this.videoObj){
				     this.videoObj[i].detachMedia()
			     }
		     }*/
		     /*this.remainArrCopy=Object.assign([],this.remainArr)
		     this.remainArrCopy.map(item=>{
			     this.videoObj['video'+item.id].stopLoad()
             })*/
		     if(this.flag === 0){
		          this.remainArr = this.remainArr.concat(this.playVideoArr)

              }else {
		          this.remainArr=this.remainArr.concat(this.playVideoArr)
		          this.playVideoArr = this.remainArr.splice(1,this.number)
		          this.remainArr = this.remainArr.concat(this.playVideoArr)
              }
		     this.remainArr.map(item=>{
			     this.videoObj['video'+item.id].stopLoad()
		     })
		      this.videoInit()
              this.lunboInit()
	      },this.time*1000)

      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
.video{
  width: 400px;
    float: left;
}
</style>
