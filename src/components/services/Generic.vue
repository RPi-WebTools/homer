<template>
  <div>
    <div
      class="card"
      :style="`background-color:${item.background};`"
      :class="item.class"
    >
      <a :href="item.url" :target="item.target" rel="noreferrer">
        <div class="card-content">
          <div :class="mediaClass">
            <slot name="icon">
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
            </slot>
            <div class="media-content">
              <slot name="content">
              <p class="title is-4">{{ item.name }}</p>
              <p class="subtitle is-6" v-if="item.subtitle">
                {{ item.subtitle }}
              </p>
              </slot>
            </div>
            <slot name="indicator" class="indicator">
              <div class="heartbeat" :class="dockerHeartbeat" v-if="item.docker_host && item.docker_name">
                {{ dockerHeartbeat | upperCase }}
              </div>
            </slot>
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
import service from "@/mixins/service.js";

export default {
  name: "Generic",
  mixins: [service],
  props: {
    item: Object,
  },
  data: () => ({
    dockerHeartbeat: 'offline'
  }),
  created: function () {
    if (this.item.docker_host && this.item.docker_name) {
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
    }
  },
  computed: {
    mediaClass: function () {
      return { media: true, "no-subtitle": !this.item.subtitle };
    },
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
.heartbeat {
  font-size: 0.8rem;
  color: var(--text-title);
  align-self: flex-start;

  &.online:before, %online-before {
    background-color: #94e185;
    border-color: #78d965;
    box-shadow: 0 0 4px 1px #94e185;
  }

  &.offline:before {
    background-color: #c9404d;
    border-color: #c42c3b;
    box-shadow: 0 0 4px 1px #c9404d;
  }

  &.unknown:before {
    background-color: #c9c740;
    border-color: #ccc935;
    box-shadow: 0px 0px 4px 1px #c9c740;
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
