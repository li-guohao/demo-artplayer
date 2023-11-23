<script setup lang="ts">
import Artplayer from "artplayer";
import { onMounted, onUnmounted, ref } from "vue";

var video = {
  url: "http://localhost:9999/files/2023/11/23/f6c66840fb3b4ff5ba38afca3639f993.mkv",
  poster: "",
  subtitles: [
    {
      name: "简中",
      url: "http://localhost:9999/files/2023/11/23/6c0d3706c6314e6892cccff546f1c220.ass",
      default: true,
    },
    {
      name: "繁中",
      url: "http://localhost:9999/files/2023/11/23/6fbaa102588a47bb839df7d98ec2f450.ass",
      default: false,
    },
  ],
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
    subtitleOffset: true,
    miniProgressBar: true,
    mutex: true,
    backdrop: true,
    playsInline: true,
    autoPlayback: true,
    airplay: true,
    theme: "#23ade5",
    lang: navigator.language.toLowerCase(),
    moreVideoAttr: {
      crossOrigin: "anonymous",
    },
    settings: [
      {
        width: 200,
        html: "Subtitle",
        tooltip: "Bilingual",
        // icon: '<img width="22" heigth="22" src="/assets/img/subtitle.svg">',
        selector: [
          {
            html: "Display",
            tooltip: "Show",
            switch: true,
            onSwitch: function (item) {
              item.tooltip = item.switch ? "Hide" : "Show";
              art.value!.subtitle.show = !item.switch;
              return !item.switch;
            },
          },
          {
            default: video.subtitles[0].default,
            html: video.subtitles[0].name,
            url: video.subtitles[0].url,
          },
          {
            default: video.subtitles[1].default,
            html: video.subtitles[1].name,
            url: video.subtitles[1].url,
          },
        ],
        onSelect: function (item) {
          art.value!.subtitle.switch(item.url, {
            name: item.html,
          });
          return item.html;
        },
      },
      {
        html: "Switcher",
        icon: '<img width="22" heigth="22" src="/assets/img/state.svg">',
        tooltip: "OFF",
        switch: false,
        onSwitch: function (item) {
          item.tooltip = item.switch ? "OFF" : "ON";
          console.info("You clicked on the custom switch", item.switch);
          return !item.switch;
        },
      },
      {
        html: "Slider",
        icon: '<img width="22" heigth="22" src="/assets/img/state.svg">',
        tooltip: "5x",
        range: [5, 1, 10, 0.1],
        onRange: function (item) {
          return item.range + "x";
        },
      },
    ],
    contextmenu: [
      {
        html: "Custom menu",
        click: function (contextmenu) {
          console.info("You clicked on the custom menu");
          contextmenu.show = false;
        },
      },
    ],
    // layers: [
    //   {
    //     html: '<img width="100" src="/assets/sample/layer.png">',
    //     click: function () {
    //       window.open("https://aimu.app");
    //       console.info("You clicked on the custom layer");
    //     },
    //     style: {
    //       position: "absolute",
    //       top: "20px",
    //       right: "20px",
    //       opacity: ".9",
    //     },
    //   },
    // ],
    quality: [
      {
        default: true,
        html: "SD 480P",
        url: video.url,
      },
      {
        html: "HD 720P",
        url: video.url,
      },
    ],
    thumbnails: {
      url: "/assets/sample/thumbnails.png",
      number: 60,
      column: 10,
    },
    subtitle: {
      url: "/assets/sample/subtitle.srt",
      type: "srt",
      style: {
        color: "#fe9200",
        fontSize: "20px",
      },
      encoding: "utf-8",
    },
    highlight: [
      {
        time: 15,
        text: "One more chance",
      },
      {
        time: 30,
        text: "谁でもいいはずなのに",
      },
      {
        time: 45,
        text: "夏の想い出がまわる",
      },
      {
        time: 60,
        text: "こんなとこにあるはずもないのに",
      },
      {
        time: 75,
        text: "终わり",
      },
    ],
    controls: [
      {
        position: "right",
        html: "Control",
        index: 1,
        tooltip: "Control Tooltip",
        style: {
          marginRight: "20px",
        },
        click: function () {
          console.info("You clicked on the custom control");
        },
      },
    ],
    // icons: {
    //   loading: '<img src="/assets/img/ploading.gif">',
    //   state: '<img width="150" heigth="150" src="/assets/img/state.svg">',
    //   indicator: '<img width="16" heigth="16" src="/assets/img/indicator.svg">',
    // },
  });
});
onUnmounted(() => {
  if (art.value) {
    art.value.destroy(false);
  }
});
</script>

<template>
  <div ref="artRef"></div>
</template>

<style scoped></style>
