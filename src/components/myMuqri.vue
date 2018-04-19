<template>
  <div class="main">
    <div class="ui grid">
      <div class="row centered ui big input">
        <select class="ui dropdown" v-model="surahNumber">
          <option v-for="(surah, index) in surahs" :key="index" :value="index + 1">{{surah.index}} - {{surah.title}}</option>
        </select>
      </div>
      <div class="row centered">
        <div class="ui action big input labeled">
          <div class="ui label">Verse</div>
          <input class="input_verse" v-model="verseNumber" type="number" min="1" v-bind:max="verses.count" size="3" value="1">
          <button type="button" v-if="verseNumber > 1" @click="verseNumber--" class="ui teal button">-</button>
          <button type="button" v-else class="ui disabled teal button">-</button>
          <button type="button" v-if="verseNumber < verses.count" @click="verseNumber++" class="ui teal button">+</button>
          <button type="button" v-else class="ui disabled teal button">+</button>
        </div>
      </div>
    </div>
    <div class="slate_segment ui stacked segment">
      <div class="slate_loader ui inverted">
        <div class="ui text loader">Loading</div>
        <div class="slate_text ui text">
          <p v-if="verses.verse" class="verse"><strong class>{{verses.verse["verse_" + verseNumber]}}</strong></p>
          <p v-if="translation.verse" class="translation" lang="en"><strong class>{{translation.verse["verse_" + verseNumber]}}</strong></p>
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
    axios.get('../static/resources/Translation/en_translation_1.json')
      .then(response => {
        this.translation = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
  },

  methods: {
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
      axios.get('../static/resources/Translation/en_translation_' + val + '.json')
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
    }
  }

}

$(document).ready(function () {
  $('select').dropdown()
})
</script>

<style scoped>
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
.form {
  background-color: lightblue;
}
.verse {
  text-align: right;
  font-size: 22pt;
}
.input_verse {
  width: 80px;
  max-width: 80px;
  min-width: 80px;
}
.translation[lang='en'] {
  font-size: 10pt;
  text-align: left;
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
</style>
