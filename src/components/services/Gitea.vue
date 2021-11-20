<template>
  <Generic :item="item">
    <template #content>
      <p class="title is-4">{{ item.name }}</p>
      <p class="subtitle is-6">
        <template v-if="item.subtitle">
          {{ item.subtitle }}
        </template>
        <template v-else-if="api">
          {{ notifications }} new notification<template v-if="notifications != 1">s</template>
        </template>
      </p>
    </template>
  </Generic>
</template>

<script>
import Generic from "./Generic.vue";

export default {
  name: "Gitea",
  components: {
    Generic,
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
        .catch((e) => console.error(e));
    },
  },
};
</script>

<style scoped lang="scss">
</style>
