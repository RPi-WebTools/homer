<template>
  <div class="heartbeat" :class="dockerHeartbeat">
    {{ dockerHeartbeat | upperCase }}
  </div>
</template>

<script>
export default {
  name: "ServiceHeartbeat",
  props: {
    item: Object,
  },
  data: () => ({
    dockerHeartbeat: 'offline'
  }),
  created: function () {
    let that = this;
    this.checkOffline();

    document.addEventListener(
      "visibilitychange",
      function () {
        if (document.visibilityState == "visible") {
          that.checkOffline();
        }
      },
      false
    );
  },
  methods: {
    checkOffline: function () {
      let that = this;
      return fetch("https://" + this.item.docker_host + "/containers/" + this.item.docker_name + "/json")
        .then((response) => response.json())
        .then(function (data) {
          if (data.State.Running) {
            that.dockerHeartbeat = 'online';
          }
          else {
            that.dockerHeartbeat = 'offline';
          }
        })
        .catch(function () {
          that.dockerHeartbeat = 'offline';
        })
        .finally(function () {
          that.$emit("network-status-update", that.dockerHeartbeat);
        });
    },
  },
  filters: {
    upperCase: function (raw) {
      let upper = [];
      raw.split(" ").forEach((word) => {
        upper.push(word.toUpperCase());
      });
      return upper.join(" ");
    }
  },
};
</script>

<style scoped lang="scss">
.heartbeat {
  font-size: 0.8rem;
  color: var(--text-title);
  align-self: flex-start;

  &.online:before {
    background-color: #94e185;
    border-color: #78d965;
    box-shadow: 0 0 4px 1px #94e185;
  }

  &.offline:before {
    background-color: #c9404d;
    border-color: #c42c3b;
    box-shadow: 0 0 4px 1px #c9404d;
  }

  &:before {
    content: " ";
    display: inline-block;
    width: 10px;
    height: 10px;
    margin-right: 10px;
    border: 1px solid #000;
    border-radius: 10px;
  }
}
</style>