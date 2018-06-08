<template>
  <div class="hello">
   <div class="video" v-for="video in videoData">
     <video :ref="'video'+video.id" controls="controls" autoplay></video>
     <button @click="stop(video.id)">暂停</button>
     <button @click="play(video.id)">播放</button>
   </div>
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
      ],
      videoObj:{}
    }
  },
  mounted(){
	  this.$nextTick(()=>{
		  if(Hls.isSupported()) {
		  	this.videoData.map((item)=>{
			    console.log('video'+item.id)
			    this.videoObj['video'+item.id] = new Hls();
                let video =this.$refs['video'+item.id][0];
			    this.videoObj['video'+item.id].loadSource(item.url);
			    this.videoObj['video'+item.id].attachMedia(video);
			    this.videoObj['video'+item.id].on(Hls.Events.MANIFEST_PARSED,function() {
                     video.play();
                 });
            });
			  /*let hls = new Hls();
			  hls.loadSource('https://video-dev.github.io/streams/x36xhzz/x36xhzz.m3u8');
			  hls.attachMedia(video);
			  hls.on(Hls.Events.MANIFEST_PARSED,function() {
				  video.play();
			  });*/
		  }
      })
  },
  methods:{
	  stop(id){
		  this.videoObj['video'+id].stopLoad()
      },
	  play(id){
		  this.videoObj['video'+id].startLoad()
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
#video{
  width: 400px;
}
</style>
