<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="bg-white text-grey-10" bordered>
      <q-toolbar>
        <q-btn active-color="primary" class="large-scree-only" to="/" size="18px" icon="eva-home-outline" flat dense
               round/>
        <q-separator class="large-scree-only" vertical spaced/>
        <q-toolbar-title class="text-grand-hotel text-bold">
          Quasagram
        </q-toolbar-title>
        <q-separator class="large-scree-only" vertical spaced/>
        <q-btn active-color="primary" class="large-scree-only" to="/camera" size="18px" icon="eva-camera-outline" flat
               dense round/>
      </q-toolbar>
    </q-header>


    <q-footer class="bg-white small-scree-only" bordered>
      <q-tabs class="text-grey-10" active-color="primary" indicator-color="transparent">
        <q-route-tab to="/" name="home" icon="eva-home-outline"/>
        <q-route-tab to="/camera" name="camera" icon="eva-camera-outline"/>
      </q-tabs>
    </q-footer>

    <q-page-container>
      <router-view/>
    </q-page-container>

  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',
  data() {
    return {}
  }
}
</script>

<style lang="scss">
.q-header {
  .q-toolbar {
    @media (min-width: $breakpoint-sm-min) {
      height: 77px;
    }

    .q-toolbar__title {
      font-size: 40px;
      @media (max-width: $breakpoint-xs-max) {
        text-align: center;
      }
    }

  }
}

.q-footer {
  .q-tab__icon {
    font-size: 35px;
  }
}
</style>
