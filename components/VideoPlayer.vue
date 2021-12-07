
<template>
  <div>
    <video-js
      ref="videoPlayer"
      id="videojs-event-tracking-player"
      class="video-js vjs-default-skin vjs-big-play-centered"
      width="950"
      height="640"
      poster="../assets/placeholders/ph-venice.png"
      preload="auto"
      crossorigin="true"
      controls
    >
      <source   src="https://videosbiites2021.s3.eu-central-1.amazonaws.com/videos/test/secret-escape-love-road-trips-8-video.m3u8" type="application/x-mpegURL"   />

      <!-- <track label="English" kind="subtitles" srclang="en" src='../secret-escape-love-road-trips-8-video.vtt' default /> -->
      <p class="vjs-no-js">
        To view this video please enable JavaScript, and consider upgrading to a
        web browser that
        <a href="https://videojs.com/html5-video-support/" target="_blank"
          >supports HTML5 video</a
        >
      </p>
    </video-js>
    <div id="console">
      <p>{{ logsData }}</p>
    </div>
  </div>
</template>

<script>
import videojs from "video.js";
import "videojs-event-tracking";
import { getAPI } from '../axios-api';
import 'videojs-contrib-hls';



export default {
  props:["src"],
  name: "VideoPlayer",
  methods: {
    log(name, data) {
      var currentTime = new Date().toLocaleString();
      const json = {
        video_id: '70873751', 
        date: currentTime,
        [name]: data,
      };
      console.log(json);
      getAPI
        .post("test/", json, {headers: {
                'Content-Type': 'application/json',
                accept: 'application/json',
            },})
        .then(() => console.log("sent"))
        .catch((error) => console.log(error));
      this.logsData.push(json);
    },
  },
  data() {
    return {
      logsData: [],
      player: null,
      videoOptions: {
        autoplay: false,
        controls: true,
        plugins: {
          eventTracking: {
            PauseTracking: true,
            BufferTracking: true,
            PercentileTracking: true,
            PlayTracking: true,
            SeekTracking: true,
            PerformanceTracking: true,
          },
        },
        // poster: "https://vjs.zencdn.net/v/oceans.png",
        // sources: [
        //   { src: "https://vjs.zencdn.net/v/oceans.mp4", type: "video/mp4" },
        //   { src: "https://vjs.zencdn.net/v/oceans.webm", type: "video/webm" },
        // ],
      },
    };
  },
  mounted() {
    const element = this.$refs["videoPlayer"];
    videojs(element, {fluent:true});
    this.player = videojs(
      this.$refs.videoPlayer, 
      this.videoOptions,
      function onPlayerReady() {
        console.log("onPlayerReady", this);
      }
    );
    this.player.ready(() => {
      console.log("Player: OnReady");
      this.player.eventTracking({
        performance: (data) => {
          this.log("tracking:performance", data);
        },
        /*
        // optional configuration to consider buffering while user is scrubbing on the video player.
        bufferingConfig: {
          includeScrub: true
        }*/
      });
      // this.player.on("tracking:firstplay", (e, data) => this.log(e.type, data));
      this.player.on("tracking:firstplay", (e, data) => this.log(e.type, data));
      this.player.on("tracking:pause", (e, data) => this.log(e.type, data));
      this.player.on("tracking:first-quarter", (e, data) =>
        this.log(e.type, data)
      );
      this.player.on("tracking:second-quarter", (e, data) =>
        this.log(e.type, data)
      );
      this.player.on("tracking:third-quarter", (e, data) =>
        this.log(e.type, data)
      );
      this.player.on("tracking:fourth-quarter", (e, data) =>
        this.log(e.type, data)
      );
      this.player.on("tracking:buffered", (e, data) => this.log(e.type, data));
      this.player.on("tracking:performance", (e, data) =>
        this.log(e.type, data)
      );
      this.player.on("tracking:seek", (e, data) => this.log(e.type, data));
    });
  },
  beforeDestroy() {
    if (this.player) {
      this.player.dispose();
    }
  },
};
</script>
<style lang="postcss">
@import url("https://vjs.zencdn.net/7.17.0/video-js.css");
</style>

<style>
#console {
  display: block;
  background-color: #2e2e2e;
  border-radius: 5px;
  padding: 10px;
  margin: 10px 0;
  width: 100%;
  max-width: 950px;
}

#console p {
  color: #9a9a9a;
  margin: 2px 0;
}
</style>
