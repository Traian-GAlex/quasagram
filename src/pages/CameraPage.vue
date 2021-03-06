<template>
  <q-page class="constrain-more q-pa-md">
    <div class="camera-frame q-pa-md" v-show="hasCameraSupport || imageCaptured">
      <video
        v-show="!imageCaptured "
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
             v-if="hasCameraSupport"
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

      <q-file outlined
              v-if="!hasCameraSupport" dense
              v-show="!imageCaptured"
              label="Choose an image..."
              v-model="uploadedImage"
              accept="image/*"
              @input="captureImageFallback">
        <template v-slot:prepend>
          <q-icon name="eva-attach-outline"/>
        </template>
      </q-file>

      <div class="row justify-center q-pa-md">
        <q-input class="col col-sm-10" v-model="post.caption" label="Caption" dense/>
      </div>
      <div class="row justify-center q-pa-md">
        <q-input :loading="locationLoading" class="col  col-sm-10" v-model="post.location" label="Location" dense>
          <template v-slot:append>
            <q-btn
              v-if="!locationLoading && geolocationSupported"
              :disable="disableLocationButton"
              round dense flat icon="eva-navigation-2-outline"
              @click="getLocation"/>
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
      hasCameraSupport: true,
      uploadedImage: [],
      imageCaptured: false,
      locationLoading: false,

      disableLocationButton: false,
      disableLocationButtonHandler: null,
    }
  },
  computed: {
    geolocationSupported() {
      return ('geolocation' in navigator);
    }
  },
  methods: {
    resetPost() {
      this.post = {
        id: uid(),
        caption: '',
        location: '',
        photo: null,
        date: Date.now(),
      };
      this.uploadedImage = [];
    },
    initCamera() {
      this.imageCaptured = false;

      // you need secure connection in production for this
      // use localhost for development
      navigator.mediaDevices.getUserMedia({video: true})
        .then(stream => {
          this.$refs.video.srcObject = stream;
        })
        .catch(error => {
          console.log(error);
          this.hasCameraSupport = false;
        })

    },
    closeCamera() {
      this.$refs.video.srcObject.getVideoTracks().forEach(track => {
        track.stop()
      })
    },
    captureImage() {
      let video = this.$refs.video;
      let canvas = this.$refs.canvas;
      canvas.width = video.getBoundingClientRect().width;
      canvas.height = video.getBoundingClientRect().height;
      let context = canvas.getContext('2d');
      context.drawImage(video, 0, 0, canvas.width, canvas.height);
      this.post.photo = this.dataURItoBlob(canvas.toDataURL());
      this.post.id = uid();
      this.post.date = Date.now();
      this.imageCaptured = true;
      this.closeCamera();
      this.getLocation();
    },
    resetCanvas() {
      this.imageCaptured = false;
      this.resetPost();
      this.initCamera();

    },
    captureImageFallback(file) {
      console.log("file ", file);

      let canvas = this.$refs.canvas;
      let context = canvas.getContext("2d");

      let reader = new FileReader();
      reader.onload = event => {
        let img = new Image();
        img.onload = () => {
          canvas.width = img.width;
          canvas.height = img.height;
          context.drawImage(img, 0, 0);
          this.imageCaptured = true;
          this.getLocation();
        }
        img.src = event.target.result;
      }
      reader.readAsDataURL(file);
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
      return new Blob([ab], {type: mimeString});
    },
    getLocation() {
      this.locationLoading = true;
      navigator.geolocation.getCurrentPosition(position => {
        this.getCityAndCountry(position);
      }, error => this.locationError(), {timeout: 7000})
    },
    getCityAndCountry(position) {
      // https://geocode.xyz/41.3189957000,2.0746469000?json=1
      let apiUrl = `https://geocode.xyz/${position.coords.latitude},${position.coords.longitude}?geoit=json`

      this.$axios.get(apiUrl).then(result => {
        this.locationSuccess(result)
      }).catch(err => {
        console.log(err);
        this.locationError()
      })
    },
    locationSuccess(result) {
      this.post.location = result.data.city
      if (result.data.country) {
        this.post.location += `, ${result.data.country}`
      }
      console.clear();
      this.locationLoading = false
      this.doDisableLocationButton();
    },
    locationError() {
      this.locationLoading = false
      this.doDisableLocationButton();
      this.$q.dialog({
        title: 'Error',
        message: 'Could not find your location.'
      })

    },
    doDisableLocationButton() {
      this.disableLocationButton = true;
      this.disableLocationButtonHandler = setTimeout(() => {
        this.disableLocationButton = false;
        clearInterval(this.disableLocationButtonHandler);
      }, 1500)

    },
  },
  mounted() {
    this.initCamera();
  }
  ,
  beforeDestroy() {
    if (this.hasCameraSupport) {
      this.closeCamera();
    }
  }
}
</script>

<style lang="scss">
.camera-frame {
  border: 2px solid $grey-10;
  border-radius: 10px;
}
</style>
