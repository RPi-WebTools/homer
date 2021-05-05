<template>
  <div>
    <div
      class="card"
      :style="`background-color:${item.background};`"
      :class="item.class"
    >
      <a :href="item.url" :target="item.target" rel="noreferrer">
        <div class="card-content">
          <div class="media">
            <div v-if="item.logo" class="media-left">
              <figure class="image is-48x48">
                <img :src="item.logo" :alt="`${item.name} logo`" />
              </figure>
            </div>
            <div v-if="item.icon" class="media-left">
              <figure class="image is-48x48">
                <i style="font-size: 35px" :class="['fa-fw', item.icon]"></i>
              </figure>
            </div>
            <div class="media-content">
              <p class="title is-4">{{ item.name }}</p>
              <p class="subtitle is-6">
                <template v-if="item.subtitle">
                  {{ item.subtitle }}
                </template>
                <template v-else-if="api">
                  {{ notifications }} new notification<template v-if="notifications != 1">s</template>
                </template>
              </p>
            </div>
            <ServiceHeartbeat v-if="api" v-bind:item="item" />
          </div>
          <div class="tag" :class="item.tagstyle" v-if="item.tag">
            <strong class="tag-text">#{{ item.tag }}</strong>
          </div>
        </div>
      </a>
    </div>
  </div>
</template>

<script>
import ServiceHeartbeat from "../ServiceHeartbeat.vue";

export default {
  name: "Gitea",
  components: {
    ServiceHeartbeat
  },
  props: {
    item: Object,
  },
  data: () => ({
    api: {
      new: 0,
    }
  }),
  computed: {
    notifications: function () {
      if (this.api) {
        return this.api.new;
      }
      return 0;
    },
  },
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      const url = `${this.item.url}/api/v1/notifications/new?access_token=${this.item.token}`;
      this.api = await fetch(url)
        .then((response) => response.json())
        .catch((e) => console.log(e));
    },
  },
};
</script>

<style scoped lang="scss">
.media-left {
  .image {
    display: flex;
    align-items: center;
  }

  img {
    max-height: 100%;
  }
}
</style>
