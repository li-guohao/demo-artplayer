<!-- try fail for switch audio for multi audio trackers video in html video tag -->
<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import Artplayer from "artplayer";

var video = {
  url: "/files/2023/11/26/54c095622db7444cb30f3ab26e50ec2d.mkv",
  poster: "",
};

var artRef = ref();
var art = ref<Artplayer>();

onMounted(() => {
  art.value = new Artplayer({
    container: artRef.value,
    url: video.url,
    poster: video.poster,
    volume: 0.5,
    isLive: false,
    muted: false,
    autoplay: false,
    pip: true,
    autoSize: true,
    autoMini: true,
    screenshot: true,
    setting: true,
    loop: true,
    flip: true,
    playbackRate: true,
    aspectRatio: true,
    fullscreen: true,
    fullscreenWeb: true,
    subtitleOffset: false,
    miniProgressBar: true,
    mutex: true,
    backdrop: true,
    playsInline: true,
    autoPlayback: true,
    airplay: true,
    theme: "skyblue",
    lang: navigator.language.toLowerCase(),
    moreVideoAttr: {
      crossOrigin: "anonymous",
    },
    plugins: [],
    settings: [],
    contextmenu: [
      {
        html: "Custom menu",
        click: function (contextmenu) {
          console.info("You clicked on the custom menu");
          contextmenu.show = false;
        },
      },
    ],
  });

  art.value.on("ready", () => {
    console.log("ready");
    console.log("art", art.value);
    console.log("art video", art.value?.video);
    // var video = art.value?.video!;
    // video.controls = true;
    // video.autoplay = true;
    // var context = new AudioContext();
    // var gainNode = context.createGain();
    // gainNode.gain.value = 1; // Change Gain Value to test
    // var filter = context.createBiquadFilter();
    // filter.type = "highpass" as BiquadFilterType; // Change Filter type to test
    // filter.frequency.value = 5040; // Change frequency to test

    // // Wait for window.onload to fire. See crbug.com/112368
    // window.addEventListener(
    //   "load",
    //   function (e) {
    //     console.log("e", e);
    //     // Our <video> element will be the audio source.
    //     var source = context.createMediaElementSource(video);
    //     source.connect(gainNode);
    //     gainNode.connect(filter);
    //     filter.connect(context.destination);
    //   },
    //   false
    // );
  });
});
onUnmounted(() => {
  if (art.value) {
    art.value.destroy(false);
  }
});
</script>

<template>
  <div align="center">
    <div style="width: 70%; min-height: 500px">
      <div ref="artRef" style="width: 100%; height: 700px"></div>
    </div>
  </div>
</template>

<style scoped></style>
