<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import Artplayer from "artplayer";
import SubtitlesOctopus from "./assets/js/JavascriptSubtitlesOctopus/subtitles-octopus";

var video = {
  url: "/files/2023/11/23/f6c66840fb3b4ff5ba38afca3639f993.mkv",
  poster: "",
  // ass subtitles support: https://github.com/zhw2590582/ArtPlayer/issues/239#issuecomment-1060243794
  subtitles: [
    {
      name: "简中",
      url: "/files/2023/11/23/6c0d3706c6314e6892cccff546f1c220.ass",
      default: true,
    },
    {
      name: "繁中",
      url: "/files/2023/11/23/6fbaa102588a47bb839df7d98ec2f450.ass",
      default: false,
    },
  ],
};

var fonts = [
  "/static/fonts/A-OTF-Jun34Pro-Medium.otf",
  "/static/fonts/DFHanziPen-W5 & DFPHanziPen-W5 & DFGHanziPen-W5.ttc",
  "/static/fonts/DFMaruMoji-SL & DFPMaruMoji-SL & DFGMaruMoji-SL.ttc",
  "/static/fonts/FOT-SeuratProN-M.otf",
  "/static/fonts/FZLanTYK_Zhun.TTF",
  "/static/fonts/华康少女文字W5 & 华康少女文字W5(P).ttc",
  "/static/fonts/华康翩翩体W5 & 华康翩翩体W5P.ttc",
  "/static/fonts/方正兰亭中黑_GBK.ttf",
  "/static/fonts/方正兰亭圆_GBK.ttf",
  "/static/fonts/方正兰亭圆_GBK_粗.ttf",
  "/static/fonts/方正兰亭粗黑_GBK.ttf",
  "/static/fonts/方正兰亭细黑_GBK.ttf",
  "/static/fonts/方正兰亭黑_GBK.ttf",
  "/static/fonts/方正准圆_GBK.TTF",
  "/static/fonts/方正准雅宋_GBK.ttf",
  "/static/fonts/方正剪纸_GBK.ttf",
  "/static/fonts/方正喵鸣.ttf",
  "/static/fonts/方正康体_GBK.ttf",
  "/static/fonts/方正粗圆_GBK.TTF",
  "/static/fonts/方正粗雅宋_GBK.ttf",
  "/static/fonts/方正细圆_GBK.ttf",
  "/static/fonts/方正行楷_GBK.ttf",
  "/static/fonts/方正隶变_GBK.ttf",
  "/static/fonts/華康少女文字W5 & 華康少女文字W5(P).ttc",
  "/static/fonts/華康翩翩體W5 & 華康翩翩體W5P.ttc",
  "/static/fonts/萝莉体-DDC.yolan 常规.ttf",
];

var artRef = ref();
var art = ref<Artplayer>();
const subtitleOctopus = ref<SubtitlesOctopus>();

const artplayerPluginAss = (options: any) => {
  return (art: any) => {
    const instance = new SubtitlesOctopus({
      ...options,
      video: art.template.$video,
    });

    instance.canvasParent.style.zIndex = 20;
    art.on("destroy", () => instance.dispose());

    subtitleOctopus.value = instance;
    return {
      name: "artplayerPluginAss",
      instance: instance,
    };
  };
};

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
    plugins: [
      artplayerPluginAss({
        // debug: true,
        fonts: fonts,
        subUrl: video.subtitles[0].url,
        workerUrl: `/src/assets/js/JavascriptSubtitlesOctopus/subtitles-octopus-worker.js`,
      }),
    ],
    settings: [
      {
        width: 200,
        html: "字幕",
        tooltip: "选择",
        // icon: '<img width="22" heigth="22" src="/assets/img/subtitle.svg">',
        selector: [
          {
            html: "开启",
            tooltip: "显示",
            switch: true,
            onSwitch: function (item) {
              item.tooltip = item.switch ? "隐藏" : "显示";
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
          const newSubtitleUrl = item.url;
          subtitleOctopus.value.setTrackByUrl(newSubtitleUrl);
          return item.html;
        },
      },
      {
        html: "Switcher",
        // icon: '<img width="22" heigth="22" src="/assets/img/state.svg">',
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
        // icon: '<img width="22" heigth="22" src="/assets/img/state.svg">',
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
        html: "SD 480P",
        url: video.url,
      },
      {
        default: true,
        html: "HD 720P",
        url: video.url,
      },
    ],
    // thumbnails: {
    //   url: "/assets/sample/thumbnails.png",
    //   number: 60,
    //   column: 10,
    // },
    // subtitle: {
    //   url: "/assets/sample/subtitle.srt",
    //   type: "srt",
    //   style: {
    //     color: "#fe9200",
    //     fontSize: "20px",
    //   },
    //   encoding: "utf-8",
    // },
    // highlight: [
    //   {
    //     time: 15,
    //     text: "One more chance",
    //   },
    //   {
    //     time: 30,
    //     text: "谁でもいいはずなのに",
    //   },
    //   {
    //     time: 45,
    //     text: "夏の想い出がまわる",
    //   },
    //   {
    //     time: 60,
    //     text: "こんなとこにあるはずもないのに",
    //   },
    //   {
    //     time: 75,
    //     text: "终わり",
    //   },
    // ],
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
  <div align="center">
    <!-- <video :src="video.url" controls style="max-height: 400px"></video> -->

    <!-- <hr /> -->

    <div style="width: 70%; min-height: 500px">
      <div ref="artRef" style="width: 100%; height: 700px"></div>
    </div>
  </div>
</template>

<style scoped></style>
