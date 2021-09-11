<template>
  <q-page class="constrain q-pa-md">
    <div class="camera-frame q-pa-md">
      <img class="full-width" src="https://cdn.quasar.dev/img/parallax2.jpg" alt="">
    </div>
    <div class="text-center q-pa-md">
      <q-btn round
             size="lg"
             color="black"
             icon="eva-camera"/>
      <div class="row justify-center q-pa-md">
        <q-input class="col" v-model="text" label="Caption" dense />
      </div>
      <div class="row justify-center q-pa-md">
        <q-input class="col" v-model="text" label="Location" dense >
          <template v-slot:append>
            <q-btn round dense flat icon="eva-navigation-2-outline" />
          </template>
        </q-input>
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'CameraPage'
}
</script>

<style lang="scss">
.camera-frame {
  border: 2px solid $grey-10;
  border-radius: 10px;
}
</style>
