<template>
  <q-page class="constrain-more q-pa-md">
    <div class="camera-frame q-pa-md">
      <video
        v-show="!imageCaptured"
        ref="video"
        class="full-width"
        autoplay
      />
      <canvas
        v-show="imageCaptured"
        ref="canvas"
        class="full-width"
        height="240"
      />
    </div>
    <div class="text-center q-pa-md">
      <q-btn round
             @click="captureImage"
             v-show="!imageCaptured"
             size="lg"
             color="black"
             icon="eva-camera"/>
      <q-btn round
             @click="resetCanvas"
             v-show="imageCaptured"
             size="lg"
             color="red-5"
             icon="eva-trash"/>
      <div class="row justify-center q-pa-md">
        <q-input class="col col-sm-10" v-model="post.caption" label="Caption" dense/>
      </div>
      <div class="row justify-center q-pa-md">
        <q-input class="col  col-sm-10" v-model="post.location" label="Location" dense>
          <template v-slot:append>
            <q-btn round dense flat icon="eva-navigation-2-outline"/>
          </template>
        </q-input>
      </div>
      <div class="row justify-center q-pt-lg">
        <q-btn unelevated rounded color="primary" label="Post Image"/>
      </div>
    </div>
  </q-page>
</template>

<script>
import {uid} from 'quasar'

require('md-gum-polyfill')

export default {
  name: 'CameraPage',
  data() {
    return {
      post: {
        id: uid(),
        caption: '',
        location: '',
        photo: null,
        date: Date.now(),
      },
      imageCaptured: false,
    }
  },

  methods: {
    resetPost(){
      this.post = {
        id: uid(),
        caption: '',
        location: '',
        photo: null,
        date: Date.now(),
      };
    },
    initCamera() {
      this.imageCaptured = false;
      try {
        // you need secure connection in production for this
        // use localhost for development
        navigator.mediaDevices.getUserMedia({video: true})
          .then(stream => {
            this.$refs.video.srcObject = stream;
          })
          .catch(error => {
            console.log(error);
          })

      } catch (e) {
        alert(e);
      }

    },
    closeCamera() {
      console.log('closeCamera');
    },
    captureImage(){
      let video = this.$refs.video;
      let canvas = this.$refs.canvas;
      canvas.width= video.getBoundingClientRect().width;
      canvas.height= video.getBoundingClientRect().height;
      let context = canvas.getContext('2d');
      context.drawImage(video, 0, 0, canvas.width, canvas.height);
      this.post.photo = this.dataURItoBlob(canvas.toDataURL());
      this.post.id = uid();
      this.post.date = Date.now();
      this.imageCaptured = true;
     },
    resetCanvas(){
      this.imageCaptured = false;
      this.resetPost();
    },
    dataURItoBlob(dataURI) {
      // convert base64 to raw binary data held in a string
      // doesn't handle URLEncoded DataURIs - see SO answer #6850276 for code that does this
      let byteString = atob(dataURI.split(',')[1]);

      // separate out the mime component
      let mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]

      // write the bytes of the string to an ArrayBuffer
      let ab = new ArrayBuffer(byteString.length);

      // create a view into the buffer
      let ia = new Uint8Array(ab);

      // set the bytes of the buffer to the correct values
      for (let i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
      }

      // write the ArrayBuffer to a blob, and you're done
      // let blob = new Blob([ab], {type: mimeString});
      return new Blob([ab], {type: mimeString});;

    }
  },
  mounted() {
    this.initCamera();
  },
  destroyed() {
    this.closeCamera();
  }
}
</script>

<style lang="scss">
.camera-frame {
  border: 2px solid $grey-10;
  border-radius: 10px;
}
</style>
