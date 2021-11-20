<template>
  <Generic :item="item">
    <template #content>
      <p class="title is-4">{{ item.name }}</p>
      <p class="subtitle is-6">
        <template v-if="item.subtitle">
          {{ item.subtitle }}
        </template>
        <template v-else-if="api">
          {{ percentage }}&percnt; blocked
        </template>
      </p>
    </template>
    <template #indicator v-if="api">
      <div class="heartbeat" :class="protection">
        {{ protection | upperCase }}
      </div>
    </template>
  </Generic>
</template>

<script>
import service from "@/mixins/service.js";
import Generic from "./Generic.vue";

export default {
  name: "PiHole",
  mixins: [service],
  components: {
    Generic,
  },
  props: {
    item: Object,
  },
  data: () => ({
    api: {
      status: "",
      ads_percentage_today: 0,
    },
  }),
  computed: {
    percentage: function () {
      if (this.api) {
        return this.api.ads_percentage_today.toFixed(1);
      }
      return "";
    },
    protection: function () {
      if (this.api) {
        return this.api.status;
      } else return "unknown";
    },
  },
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      const url = `${this.item.url}/api.php`;
      this.api = await fetch(url)
        .then((response) => response.json())
        .catch((e) => console.error(e));
    },
  },
};
</script>

<style scoped lang="scss">
.heartbeat.enabled:before {
  background-color: #94e185;
  border-color: #78d965;
  box-shadow: 0 0 4px 1px #94e185;
}
.heartbeat.disabled:before {
  background-color: #c69c6d;
  border-color: #c69c6d;
  box-shadow: 0 0 4px 1px #c69c6d;
}
</style>
