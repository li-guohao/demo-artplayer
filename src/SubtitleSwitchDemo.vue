<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import Artplayer from "artplayer";
import SubtitlesOctopus from "@/assets/js/JavascriptSubtitlesOctopus/subtitles-octopus";

const subtitlesOctopusWorkJsPath =
  "/src/assets/js/JavascriptSubtitlesOctopus/subtitles-octopus-worker.js";

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
const subtitleOctopus = ref();
const tmpSubtitleOctopusSubUrl = ref("");

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
    plugins: [
      artplayerPluginAss({
        // debug: true,
        fonts: fonts,
        subUrl: video.subtitles[0].url,
        workerUrl: subtitlesOctopusWorkJsPath,
      }),
    ],
    settings: [
      {
        width: 200,
        html: "字幕",
        tooltip: "选择",
        icon: '<img width="22" heigth="22" src="/svg/subtitle.svg">',
        selector: [
          {
            html: "开启",
            tooltip: "显示",
            switch: true,
            onSwitch: function (item) {
              item.tooltip = item.switch ? "隐藏" : "显示";
              // art.value!.subtitle.show = !item.switch;
              if (item.switch) {
                tmpSubtitleOctopusSubUrl.value = subtitleOctopus.value.subUrl;
                subtitleOctopus.value.freeTrack();
              } else {
                subtitleOctopus.value.setTrackByUrl(
                  tmpSubtitleOctopusSubUrl.value
                );
                subtitleOctopus.value.setSubUrl(tmpSubtitleOctopusSubUrl.value);
              }
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
          tmpSubtitleOctopusSubUrl.value = newSubtitleUrl;
          subtitleOctopus.value.setTrackByUrl(newSubtitleUrl);
          subtitleOctopus.value.setSubUrl(newSubtitleUrl);
          return item.html;
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
