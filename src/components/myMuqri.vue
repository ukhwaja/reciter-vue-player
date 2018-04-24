<template>
  <div class="main">
    <div class="ui grid">
      <div class="row centered">
        <div class="ui action big input labeled">
          <div class="ui label">Surah</div>
            <select class="ui search dropdown" v-model="surahNumber">
              <option v-for="(surah, index) in surahs" :key="index" :value="index + 1">{{index + 1}} - {{surah.title}}</option>
            </select>
        </div>
      </div>
      <div class="row centered">
        <div class="ui action big input labeled">
          <div class="ui label">Verse</div>
          <select class="ui search dropdown" v-model="verseNumber">
            <option :class="index == verseNumber ? 'item active selected' : 'item'" v-for="index in verses.count" :key="index" :value="index">{{index}}</option>
          </select>
        </div>
      </div>
      <div class="row centered">
        <div class="ui big buttons teal">
          <div v-on:click="play" class="ui button" id="play-button">
            <i class="play icon"></i> Play
          </div>
          <div class="ui button">
            <i class="redo alternate icon"></i> Repeat
          </div>
          <div class="ui button">
            <i class="hourglass half icon"></i> Delay
          </div>
        </div>
      </div>
    </div>
    <audio><source :src=audioURL></audio>
    <div class="slate_segment ui stacked segment">
      <div class="slate_loader ui inverted">
        <div class="ui text loader">Loading</div>
        <div class="slate_text ui text">
          <p dir="rtl" lang="ar" v-if="verses.verse" class="verse">{{verses.verse["verse_" + verseNumber]}}<span :class="verseNumber < 10 ? 'end_verse end_verse_0' : 'end_verse end_verse_1'"> {{verseNumber}}</span></p>
          <p v-if="translation.verse" class="translation" lang="en">{{translation.verse["verse_" + verseNumber]}}</p>
        </div>
      </div>
    </div>
    <ul v-if="errors && errors.length">
      <li v-for="error of errors" :key='error'>
        {{error.message}}
      </li>
    </ul>
  </div>
</template>

<script>

import axios from 'axios'
import _ from 'lodash'

export default {
  name: 'myMuqri',
  data () {
    return {
      surahs: [],
      verses: [],
      translationLang: 'en',
      translation: [],
      errors: [],
      verseNumber: 1,
      surahNumber: 1
    }
  },

  created () {
    axios.get('../static/resources/surah.json')
      .then(response => {
        this.surahs = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
    axios.get('../static/resources/Surah/surah_1.json')
      .then(response => {
        this.verses = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
    axios.get('../static/resources/Translation/' + this.translationLang + '/' + this.translationLang + '_translation_1.json')
      .then(response => {
        this.translation = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
    $(document).ready(function () {
      $('select').dropdown()
    })
  },

  methods: {
    play: _.throttle(
      function (event) {
        if ($('audio')[0].paused) {
          $(event.target).toggleClass('active')
          $('audio')[0].play()
          var duration = $('audio')[0].duration * 1000
          var timer = setTimeout(function () {
            $(event.target).removeClass('active')
          }, duration)
        } else {
          $(event.target).removeClass('active')
          $('audio')[0].load()
          clearTimeout(timer)
        }
      }, 16
    ),
    getSurah: function (val) {
      axios.get('../static/resources/Surah/surah_' + val + '.json')
        .then(response => {
          this.verses = response.data
        })
        .catch(e => {
          this.errors.push(e)
        })
    },
    getTranslation: function (val) {
      axios.get('../static/resources/Translation/' + this.translationLang + '/' + this.translationLang + '_translation_' + val + '.json')
        .then(response => {
          this.translation = response.data
        })
        .catch(e => {
          this.errors.push(e)
        })
    },
    hideText: () => {
      $('.text .verse').hide()
      $('.text .translation').hide()
      $('.slate_loader').addClass('active dimmer')
    },
    showText: _.debounce(() => {
      $('.slate_loader').removeClass('active dimmer')
      $('.text .verse').show()
      $('.text .translation').show()
    }, 500)
  },

  watch: {
    surahNumber: function (val) {
      this.hideText()
      this.verseNumber = 1
      this.getSurah(val)
      this.getTranslation(val)
      this.showText()
    },
    audioURL: (url) => {
      console.log(url)
      $('audio')[0].load(url)
    }
  },

  computed: {
    surahAudioNumber: function () {
      var str = '' + this.surahNumber
      var pad = '000'
      var ans = pad.substring(0, pad.length - str.length) + str
      return ans
    },
    verseAudioNumber: function () {
      var str = '' + this.verseNumber
      var pad = '000'
      var ans = pad.substring(0, pad.length - str.length) + str
      return ans
    },
    audioURL: function () {
      return 'http://www.everyayah.com/data/Ibrahim_Akhdar_32kbps/' + this.surahAudioNumber + this.verseAudioNumber + '.mp3'
    }
  }

}
</script>

<style scoped>
@font-face {
  font-family: 'UthmanTN1';
  src: url('../fonts/UthmanTN1.otf') format('opentype');
}
@font-face {
  font-family: 'UthmanTN1B';
  src: url('../fonts/UthmanTN1B.otf') format('opentype');
}

input[type='number'] {
    -moz-appearance:textfield;
}
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
}
.main {
  max-width: 450px;
  margin: 0 auto;
}
.verse {
  text-align: right;
  font-size: 22pt;
  font-family: 'UthmanTN1B';
  line-height: 2em;
}
.input_verse {
  width: 80px;
  max-width: 80px;
  min-width: 80px;
}
.translation[lang='en'] {
  font-size: 14pt;
  text-align: left;
  font-family: Arial, Helvetica, sans-serif;
}
.slate_segment {
  background-color: rgb(123, 226, 209);
  min-height: 100px;
  margin: 2em;
  padding: 2em;
}
h1, h2 {
  font-weight: normal;
}
.end_verse {
  font-size: 10pt;
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  right: 10px;
  top: -4px;
  position: relative;
  white-space: nowrap;
}
.end_verse_0:before {
  content: '\06DD';
  position: absolute;
  right: -7.5px;
  top: -21px;
  font-size: 20pt;
}
.end_verse_1:before {
  content: '\06DD';
  position: absolute;
  right: -4px;
  top: -21px;
  font-size: 20pt;
}
</style>
