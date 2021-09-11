<template>
  <q-page class="constrain q-pa-md">
    <div class="row q-col-gutter-lg">
      <div class="col-12 col-sm-8 ">

        <q-card v-for="(item, index) in posts" :key="index" class="card-post q-mb-md" flat bordered>
          <q-item>
            <q-item-section avatar>
              <q-avatar>
                <img :src="item.avatar">
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label class="text-bold">{{ item.name }}</q-item-label>
              <q-item-label caption>
                {{ item.location }}
              </q-item-label>
            </q-item-section>
          </q-item>

          <q-separator/>

          <img :src="item.image">

          <q-card-section>
            <div>{{ item.caption }}</div>
            <div class="text-caption text-grey">{{ item.date | niceDate }}</div>
          </q-card-section>

        </q-card>

      </div>
      <div class="large-scree-only  col-xs-12 col-sm-4 ">
        <q-item class="fixed">
          <q-item-section avatar>
            <q-avatar size="48px">
              <img :src="posts[0].avatar">
            </q-avatar>
          </q-item-section>

          <q-item-section>
            <q-item-label class="text-bold">{{ posts[0].name }}</q-item-label>
            <q-item-label caption>
              {{ posts[0].location }}
            </q-item-label>
          </q-item-section>
        </q-item>

      </div>
      <div class="small-scree-only col-12 col-sm-4 ">
        <q-item class="">
          <q-item-section avatar>
            <q-avatar size="48px">
              <img :src="posts[0].avatar">
            </q-avatar>
          </q-item-section>

          <q-item-section>
            <q-item-label class="text-bold">{{ posts[0].name }}</q-item-label>
            <q-item-label caption>
              {{ posts[0].location }}
            </q-item-label>
          </q-item-section>
        </q-item>

      </div>
    </div>


  </q-page>
</template>

<script>
import {date} from 'quasar'

export default {
  name: 'HomePage',
  data() {
    return {
      posts: [
        {
          id: 1,
          caption: "Golden Gate Bridge",
          date: 1631283446714,
          image: "https://cdn.quasar.dev/img/parallax2.jpg",
          name: "Alexandru G. Traian",
          avatar: "https://cdn.quasar.dev/img/boy-avatar.png",
          location: "Macerata, Italy"
        },
        {
          id: 2,
          caption: "Lorem ipsum dolor",
          date: 1631283446714,
          image: "https://picsum.photos/200/300",
          name: "Alexandru G. Traian",
          avatar: "https://cdn.quasar.dev/img/boy-avatar.png",
          location: "Macerata, Italy"
        },
        {
          id: 3,
          caption: "Lorem ipsum dolor",
          date: 1631283446714,
          image: "https://picsum.photos/250/350",
          name: "Alexandru G. Traian",
          avatar: "https://cdn.quasar.dev/img/boy-avatar.png",
          location: "Macerata, Italy"
        },
      ]
    }
  },
  filters: {
    niceDate(timeStamp) {
      return date.formatDate(timeStamp, 'MMMM D, YYYY,  HH:mm');
    }
  },

}
</script>

<style lang="scss">
.card-post {
  img {
    min-height: 200px;
  }
}
</style>
