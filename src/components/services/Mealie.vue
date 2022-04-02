<template>
  <Generic :item=item>
    <template #content>
      <p class="title is-4">{{ item.name }}</p>
      <p class="subtitle is-6">
        <template v-if="item.subtitle">
          {{ item.subtitle }}
        </template>
        <template v-else-if="meal">
          Recipe Manager (today: {{ meal.name }})
        </template>
        <template v-else-if="stats">
          Recipe Manager (serving {{ stats.totalRecipes }} recipes)
        </template>
      </p>
    </template>
  </Generic>
</template>

<script>
import Generic from "./Generic.vue";

export default {
  name: "Mealie",
  components: {
    Generic,
  },
  props: {
    item: Object,
  },
  data: () => ({
    stats: null,
    meal: null,
  }),
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      if (this.item.subtitle != null) return; // omitting unnecessary ajax call as the subtitle is showing
      this.meal = await fetch(`${this.item.url}/api/meal-plans/today/`, {
        headers: {
          Authorization: "Bearer " + this.item.apikey,
          Accept: "application/json",
        },
      })
        .then(function (response) {
          if (!response.ok) {
            throw new Error("Not 2xx response");
          } else {
            if (response != null) {
              return response.json();
            }
          }
        })
        .catch((e) => console.error(e));
      this.stats = await fetch(`${this.item.url}/api/debug/statistics/`, {
        headers: {
          Authorization: "Bearer " + this.item.apikey,
          Accept: "application/json",
        },
      })
        .then(function (response) {
          if (!response.ok) {
            throw new Error("Not 2xx response");
          } else {
            return response.json();
          }
        })
        .catch((e) => console.error(e));
    },
  },
};
</script>

<style scoped lang="scss">
</style>
