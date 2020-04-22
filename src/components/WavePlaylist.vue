<template>
    <div class="butonGroup" v-show="loaded">
      <div id="top-bar" class="playlist-top-bar">
        <div class="playlist-toolbar">
          <div class="btn-group">
            <span class="btn-pause btn btn-warning" v-on:click="pause">
              <b-icon icon="pause-fill"></b-icon>
            </span>
            <span class="btn-play btn btn-success" v-on:click="start">
              <b-icon icon="play-fill"></b-icon>
            </span>
            <span class="btn-stop btn btn-danger" v-on:click="stop">
              <b-icon icon="stop-fill"></b-icon>
            </span>
          </div>
        </div>
      </div>
      <div id="playlist"></div>
    </div>
</template>
<script>
  import '../styles/playlist.scss'
  var ee = null
  export default {
    props: ['tracks', 'startTime', 'trackWidth', 'trackHeight', 'waveOutlineColor'],
    name: 'wave-playlist',
    data: function () {
      return {
        // this is time length of audio file
        duration: 0,
        loaded: false
      }
    },
    mounted: async function () {
      window.OfflineAudioContext = window.OfflineAudioContext || window.webkitOfflineAudioContext
      window.AudioContext = window.AudioContext || window.webkitAudioContext
      let audioContext = new window.AudioContext()
      let sampleRate = audioContext.sampleRate

      this.duration = await this.getMp3Secs(this.tracks[0].mp3, this.startTime)
      let customSamplesPerPixel = Math.round(this.duration * sampleRate / this.trackWidth)
      // console.log('duration: ', this.duration)

      let WaveformPlaylist = require('waveform-playlist')
      let playlist = WaveformPlaylist.init(
        {
          samplesPerPixel: customSamplesPerPixel,
          ac: audioContext,
          sampleRate: sampleRate,
          mono: true,
          timescale: true,
          waveHeight: this.trackHeight,
          container: document.getElementById('playlist'),
          state: 'cursor',
          colors: {
            waveOutlineColor: this.waveOutlineColor,
            timeColor: 'grey',
            fadeColor: 'black'
          },
          controls: {
            show: true,
            width: 200
          },
          zoomLevels: [512, customSamplesPerPixel],
          seekStyle: 'fill',
          isAutomaticScroll: true,
          isContinuousPlay: true
        }
      )

      ee = playlist.getEventEmitter()
      ee.on('finished', function () {
        // console.log('The cursor has reached the end of the selection !')
      })

      let loadTrack = this.tracks.map((track, index) => {
        return {
          src: this.tracks[index].mp3,
          name: this.tracks[index].name,
          selected: {
            start: this.startTime
          },
          start: this.startTime
        }
      })

      playlist
        .load(loadTrack)
        .then(this.loadedCallback)
    },
    beforeDestroy: function () {
      console.log('this is befroe destroy!')
    },
    methods: {
      pause: () => {
        // console.log('this is pause!')
        ee.emit('pause')
      },
      start: () => {
        // console.log('this is play!')
        ee.emit('play')
      },
      stop: () => {
        // console.log('this is stop!')
        ee.emit('stop')
      },
      getMp3Secs: (mp3file, startTime) => {
        return new Promise((resolve, reject) => {
          let audioContext = new (window.AudioContext || window.webkitAudioContext)()
          let request = new XMLHttpRequest()
          request.open('GET', mp3file, true)
          request.responseType = 'arraybuffer'
          request.onload = function () {
            audioContext.decodeAudioData(request.response, function (buffer) {
              resolve(buffer.duration + startTime)
            })
          }
          request.send()
        })
      },
      loadedCallback: function () {
        const playlistOverplays = document.getElementsByClassName('playlist-overlay')
        Object.keys(playlistOverplays).forEach((key) => {
          if (this.tracks[key].img) {
            playlistOverplays[key].style.backgroundImage = "url('" + this.tracks[key].img + "')"
          }
        })
        this.loaded = true
      }
    }
  }
</script>

<style lang='scss' scoped>
</style>
