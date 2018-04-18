<template>
  <div class="main ui grid">
    <div class="one column">
      <h2>{{verses.name}}</h2>
      <form class="form">
        <div class="ui labeled input">
          <label for="surah_input" class="ui label">Surah</label>
          <select id="surah_input" class="ui dropdown" v-model="surahNumber" type="number" min="1" max="114" length="3" value="1">
            <option v-for="(surah, index) in surahs" :key="index" :value="index + 1">{{surah.index}} - {{surah.title}}</option>
          </select>
        </div>
        <div class="ui labeled input">
          <label for="verse_input" class="ui label">Verse</label>
          <input id="verse_input" v-model="verseNumber" type="number" min="1" v-bind:max="verses.count" length="3" value="1">
        </div>
      </form>
      <div class="text">
      <p v-if="verses" class="verse"><strong class>{{verses.verse["verse_" + verseNumber]}}</strong></p>
      <p v-if="translation" lang="en" class="translation"><strong class>{{translation.verse["verse_" + verseNumber]}}</strong></p>
      </div>
      <ul v-if="errors && errors.length">
        <li v-for="error of errors" :key='error'>
          {{error.message}}
        </li>
      </ul>
    </div>
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
        // JSON responses are automatically parsed.
        this.surahs = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
    axios.get('../static/resources/Surah/surah_1.json')
      .then(response => {
        // JSON responses are automatically parsed.
        this.verses = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
    axios.get('../static/resources/Translation/en_translation_1.json')
      .then(response => {
        // JSON responses are automatically parsed.
        this.translation = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
  },

  methods: {
    getSurah: _.debounce(
      function (val) {
        axios.get('../static/resources/Surah/surah_' + val + '.json')
          .then(response => {
            this.verses = response.data
          })
          .catch(e => {
            this.errors.push(e)
          })
      }, 500
    ),
    getTranslation: _.debounce(
      function (val) {
        axios.get('../static/resources/Translation/en_translation_' + val + '.json')
          .then(response => {
            this.translation = response.data
          })
          .catch(e => {
            this.errors.push(e)
          })
      }, 500
    )
  },

  watch: {
    surahNumber: function (val) {
      console.log(val)
      this.getSurah(val)
      this.getTranslation(val)
    }
  }

}

require('semantic-ui-css/semantic.css')
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
  width: 50%;
  margin: 0 auto;
}
.verse {
  text-align: right;
  font-size: 22pt;
}
.translation[lang='en'] {
  font-size: 10pt;
  text-align: left;
}
.text {
  margin: 0 auto;
  display: block;
  max-width: 380px;
  min-height: 300px;
  background-color: rgb(169, 240, 208);
  padding: 2em;
  border-radius: 1em;
  transition: all 3ms ease;
}
.form {
  margin: 2em auto;
}
h1, h2 {
  font-weight: normal;
}
a {
  color: #42b983;
}
</style>
