<!-- 歌手详情页 -->
<template>
  <transition name="slide">
    <music-list :songs="songs" :title="title" :bg-image="bgImage"></music-list>
  </transition>
</template>

<script>
import { mapGetters } from 'vuex'
import { getSingerDetail } from 'api/singer.js'
import {ERR_OK} from 'api/config'
import {createSong} from 'common/js/song'
import MusicList from 'components/music-list/music-list'
import {getMusic} from 'api/music'
export default {
  data() {
    return {
      songs: []
    }
  },
  created() {
    this._getSingerDetail()
  },
  components: {
    MusicList
  },
  watch: {},
  computed: {
    title() {
      return this.singer.name
    },
    bgImage() {
      return this.singer.avatar
    },
    ...mapGetters([
      'singer'
    ])
  },
  mounted() {},
  methods: {
    _getSingerDetail() {
      if (!this.singer.id) {
        this.$router.push('/singer')
        return
      }
      getSingerDetail(this.singer.id).then((res) => {
        if (res.code === ERR_OK) {
          this.songs = this._normalizeSong(res.data.list)
        }
      })
    },
    _normalizeSong(list) {
      let ret = []
      list.forEach((item) => {
        let {musicData} = item
        if (musicData.songid && musicData.albummid) {
          getMusic(musicData.songmid).then((res) => {
            if (res.code === ERR_OK) {
              const songVKey = res.data.items[0].vkey
              musicData.songVKey = songVKey
              ret.push(createSong(musicData))
            }
          })
        }
      })
      console.log(ret)
      return ret
    }
  }
}

</script>
<style scoped lang="stylus" rel="stylesheet/stylus">
.slide-enter-active, .slide-leave-active {
  transition: all 0.5s
}
.slide-enter, .slide-leave-to {
  transform: translate3d(100%, 0, 0)
}
</style>
