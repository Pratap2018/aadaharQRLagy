<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <video id="videoInp" style="display: none;"></video>
    <canvas id="outPutCanvas"></canvas>
    <Button @click="startVideo" id="startVideo">Scan</Button>

  </div>
</template>

<script>
/* eslint-disable */
import jsqr from 'jsqr'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      imageData: null
    }
  },
  mounted() {
    setInterval(() => {
      if (this.imageData !== null) {
        console.log('process');
         this.processQr(this.imageData)
      }
    }, 500)
  },
  methods: {

    async processQr(imageData) {
      return new Promise((resolve, reject) => {
          const code = jsqr(imageData.data, imageData.height, imageData.width)
          this.imageData = null
          if (code) {
            console.log(code.data)
            alert(code.data)
          
          }
          resolve()
      })
   

    },

    async displayOnCanvas() {
      var video = document.getElementById('videoInp');

      var canvas = document.getElementById('outPutCanvas');
      canvas.width = 1080;
      canvas.height = 1920;
      var context = canvas.getContext('2d');
      //  display video on canvas sequre 200*200
      context.drawImage(video, 0, 0, canvas.width,canvas.height);

      // draw rectangle on video 
      context.beginPath();
      context.rect(canvas.width/2 -400,canvas.height/2 -400, 800, 800);
      context.lineWidth = 2;
      context.strokeStyle = 'white';
      context.stroke();



      const imageData = context.getImageData(canvas.width/2 -399,canvas.height/2 -399,800,800);
      // console.log(imageData);
      // every 5 second process qr code non blocking
      this.imageData = imageData;
      requestAnimationFrame(this.displayOnCanvas)

    }


    , startVideo() {
      var video = document.getElementById('videoInp');
      const $this = this;
      if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        navigator.mediaDevices.getUserMedia({
          video: {
            width: { min: 640, ideal: 1920, max: 1920 },
            height: { min: 480, ideal: 1080, max: 1080 },

            facingMode: 'environment',

          }
        }).then(function (stream) {
          const track = stream.getVideoTracks()[0]
          const capabilities = track.getCapabilities()
          if (capabilities.focusMode?.includes('continuous')) {
            track.applyConstraints({
              advanced: [{
                focusMode: 'continuous',
                exposureMode: 'continuous',
                whiteBalanceMode: 'continuous',
                zoom: "1"
              }]
            })
          }
          video.srcObject = stream
          video.play();
          $this.displayOnCanvas();

        });
      }
    }
  },





}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
