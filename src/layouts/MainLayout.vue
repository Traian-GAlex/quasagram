<template>
  <q-layout view="lHh Lpr lFf">
    <q-header>
      <q-toolbar>
        <q-toolbar-title>
          Quasagram
        </q-toolbar-title>
      </q-toolbar>
    </q-header>


    <q-footer class="bg-white" bordered>
      <q-tabs class="text-grey-10" active-color="primary" indicator-color="transparent">
        <q-route-tab to="/" name="home" icon="alarm" label="Home"/>
        <q-route-tab to="/camera" name="camera" icon="movie" label="Camera"/>
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
