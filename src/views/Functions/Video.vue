<template>
  <div>
    <div class="head">
      <label>
        视频地址
      </label>
      <input v-model="url" />
      <button @click="open">开启</button>
      <label>采用的视频插件为：{{ videoType }}</label>
    </div>
    <div class="bottom">
      <flv v-if="isFlvopen" :url="url" @close="close" />
      <video-vue v-if="isVideoopen" :url="url" @close="close" />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Flv from '@/components/Flv.vue';
import VideoVue from '@/components/Video.vue';

@Component({
  components: {
    Flv,
    VideoVue
  }
})
export default class extends Vue {
  private url: string;
  private isFlvopen: boolean;
  private isVideoopen: boolean;
  private videoType: string;

  constructor() {
    super();
    this.url = '';
    // this.url = 'http://cctvalih5ca.v.myalicdn.com/live/cctv1_2/index.m3u8';
    // this.url = 'http://vjs.zencdn.net/v/oceans.mp4';
    this.isFlvopen = false;
    this.isVideoopen = false;
    this.videoType = '';
  }

  // vue源码缓存函数
  private cached(fn: Function) {
    const cachedFn = Object.create(null);

    return function cache(str: string) {
      const hit = cachedFn[str];

      return hit || (cachedFn[str] = fn(str));
    };
  }

  private getVideoType(url: string) {
    if (url) {
      const type = url.slice(url.lastIndexOf('.') + 1);
      const urlHead = url.slice(0, 4);
      if (urlHead === 'http' && type !== 'm3u8') {
        this.isFlvopen = true;
        this.videoType = 'flv.js';
      } else {
        this.isVideoopen = true;
        this.videoType = 'video.js';
      }
    }
  }

  private open() {
    this.close();
    this.$nextTick(() => {
      const cacheGetVideoType = this.cached(this.getVideoType);
      cacheGetVideoType(this.url);
    });
  }

  private close() {
    this.isFlvopen = false;
    this.isVideoopen = false;
    this.videoType = '';
  }
}
</script>
