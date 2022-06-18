<template>
  <div class="music-player-container" :class="{ 'is-playing': isPlaying }">
    <div class="music-player">
      <div class="player-content-container">
        <h1 class="artist-name">استاد شجریان</h1>
        <!--  /.album-title -->
        <h3 class="song-title">{{ currentMusic.title }}</h3>
        <div class="time-container">
          <span>{{ currentTime }}</span>
          <span>{{ durationTime }}</span>
        </div>
        <!--  /.song-title -->
        <div class="music-player-controls">
          <div class="control-back" @click="prev"></div>
          <!--  /.control-back -->
          <div class="control-play" @click="play"></div>
          <!--  /.control-play -->
          <div class="control-forwards" @click="next"></div>
          <!--  /.control-forwards -->
        </div>
        <!--  /.music-player-controls -->
      </div>
      <!--  /.player-content-container -->
    </div>
    <!--  /.music-player -->

    <div class="album">
      <div class="album-art" :style="`background: #fff url(${currentMusic.photo}) center/cover no-repeat;`"></div>
      <!--  /.album-art -->
      <div class="vinyl" :style="`background-image: url('../../public/images/img/vinyl.png'), url(${currentMusic.photo});`"></div>
      <!--  /.vinyl -->
    </div>
    <!--  /.album-art -->
  </div>
  <!--  /.music-player -->
</template>

<script>
import { ref } from 'vue';

import musics from './musics.js';

export default {
  name: "App",

  setup() {
    const currentMusic = ref(null);
    const isPlaying = ref(false);
    const audio = ref(null);
    const currentMusicIndex = ref(0);
    const currentTime = ref('');
    const durationTime = ref('');


    currentMusic.value = musics[currentMusicIndex.value];
    audio.value = new Audio();
    audio.value.src = currentMusic.value.source;


    const play = () => {
      if(! isPlaying.value) {
        audio.value.play();
        isPlaying.value = true;
      } else {
        audio.value.pause();
        isPlaying.value = false;
      }
    };

    const next = () => {
      if(isPlaying.value) {
        play();
      }

      if(currentMusicIndex.value < musics.length - 1) {
        currentMusicIndex.value += 1;
      } else {
        currentMusicIndex.value = 0;
      }

      currentMusic.value = musics[currentMusicIndex.value];
      audio.value.src = currentMusic.value.source;

      play();
    }

    const prev = () => {
      if(isPlaying.value) {
        play();
      }

      if(currentMusicIndex.value == 0) {
        currentMusicIndex.value = musics.length - 1;
      } else {
        currentMusicIndex.value -= 1;
      }

      currentMusic.value = musics[currentMusicIndex.value];
      audio.value.src = currentMusic.value.source;

      play();
    }

    const generateTime = () => {
      let durMin = Math.floor(audio.value.duration / 60);
      let durSec = Math.floor(audio.value.duration - durMin * 60);

      let curMin = Math.floor(audio.value.currentTime / 60);
      let curSec = Math.floor(audio.value.currentTime - curMin * 60);

      if(durMin < 10) {
        durMin = '0' + durMin;
      }
      if(durSec < 10) {
        durSec = '0' + durSec;
      }
      if(curMin < 10) {
        curMin = '0' + curMin;
      }
      if(curSec < 10) {
        curSec = '0' + curSec;
      }

      durationTime.value = `${durMin}:${durSec}`;
      currentTime.value = `${curMin}:${curSec}`;
    }

    
    audio.value.onloadedmetadata = () => {
      generateTime();
    };

    audio.value.ontimeupdate = () => {
      generateTime();
    };

    return {
      currentMusic,
      isPlaying,
      play,
      next,
      prev,
      durationTime,
      currentTime
    }
  }
}
</script>

<style>
.time-container {
  display: flex;
  justify-content: space-around;
  margin-bottom: 1rem;
}
</style>