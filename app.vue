<template>
  <div>
    <video ref="video" autoplay playsinline></video>
    <canvas ref="canvas" style="display: none;"></canvas>
    <button @click="capturePicture">Capture Picture</button>
    <button @click="switchCamera">Switch Camera</button>
    <img v-if="photo" :src="photo" alt="Captured Photo" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      photo: null,
      stream: null,
      currentCamera: 'user' // 'user' for front camera, 'environment' for rear camera
    };
  },
  methods: {
    async startCamera() {
      try {
        // Stop any existing stream
        if (this.stream) {
          this.stream.getTracks().forEach(track => track.stop());
        }
        
        // Request new stream
        this.stream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: this.currentCamera }
        });
        this.$refs.video.srcObject = this.stream;
      } catch (error) {
        console.error('Error accessing camera: ', error);
        alert('Cannot access camera');
      }
    },
    capturePicture() {
      const canvas = this.$refs.canvas;
      const video = this.$refs.video;
      
      if (!canvas || !video) return;

      const context = canvas.getContext('2d');
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      context.drawImage(video, 0, 0);
      this.photo = canvas.toDataURL('image/png');
    },
    switchCamera() {
      this.currentCamera = this.currentCamera === 'user' ? 'environment' : 'user';
      this.startCamera();
    }
  },
  mounted() {
    this.startCamera();
  }
};
</script>

<style>
/* Add any styles you need here */
</style>
