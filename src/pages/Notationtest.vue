<template>
  <div>
    <WavePlaylist 
      :tracks ="tracks" 
      :startTime ="startTime" 
      :trackWidth ="trackWidth" 
      :trackHeight="trackHeight"
      :waveOutlineColor="waveOutlineColor"
      >
    </WavePlaylist>
  </div>
</template>

<script>
import WavePlaylist from '../components/WavePlaylist'

export default {
  components: {
    WavePlaylist
  },
  name: 'Home',
  data: function () {
    return {
      tracks: [
        {
          mp3: '/media/audio/track1.mp3',
          img: '/media/img/track1.jpg',
          name: 'Vocals 1'
        },
        {
          mp3: '/media/audio/track1.mp3',
          // img: '/media/img/track1.jpg',
          name: 'Vocals 1 vaweform'
        },
        {
          mp3: '/media/audio/track2.mp3',
          img: '/media/img/track2.jpg',
          name: 'Vocals 2'
        },
        {
          mp3: '/media/audio/track2.mp3',
          // img: '/media/img/track2.jpg',
          name: 'Vocals 2 vaweform'
        }
      ],
      trackHeight: 200, // must be same as the height of notation images if using those
      startTime: 1.8,
      waveOutlineColor: '#bbbbbb',
      trackWidth: 6857 - 171 * 2
    }
  }
}
</script>
