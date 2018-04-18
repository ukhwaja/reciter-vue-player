<template>
  <div class="main">
    <h2>{{verses.name}}</h2>
    <div class="text">
    <p v-if="verses" class="verse"><strong class>{{verses.verse["verse_" + verseNumber]}}</strong></p>
    <p v-if="translation" lang="en" class="translation"><strong class>{{translation.verse["verse_" + verseNumber]}}</strong></p>
    </div>
    <ul v-if="errors && errors.length">
      <li v-for="error of errors" :key='error'>
        {{error.message}}
      </li>
    </ul>
    <div class="inputs">
      <label for="surah_input">Surah</label>
      <input id="surah_input" v-model="surahNumber" type="number" min="1" max="114" length="3" value="1">
      <label for="verse_input">Verse</label>
      <input id="verse_input" v-model="verseNumber" type="number" min="1" v-bind:max="verses.count" length="3" value="1">
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
      verses: [],
      translation: [],
      errors: [],
      verseNumber: 1,
      surahNumber: 1
    }
  },

  created () {
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

  // computed: {
  //   surahNumber: {
  //     get: () => {
  //       return this.surahNumber
  //     },
  //     set: (newVal) => {
  //       console.log('../static/resources/Surah/surah_' + newVal + '.json')
  //       axios.get('../static/resources/Surah/surah_' + newVal + '.json')
  //         .then(response => {
  //           // JSON responses are automatically parsed.
  //           this.verses = response.data
  //         })
  //     }
  //   }
  // }
  // computed: {
  //   // a computed getter
  //   chosenVerseNumber: {
  //     get: () => {
  //       // `this` points to the vm instance
  //       console.log(this.chosenVerseNumber)
  //       return this.chosenVerseNumber
  //     },
  //     set: (newVal) => {
  //       this.verseNumber = newVal
  //       console.log(newVal)
  //     }
  //   }
  // }

}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.verse {
  text-align: right;
  font-size: 22pt;
}
.translation[lang='en'] {
  font-size: 10pt;
  text-align: left;
}
.text {
  width: 50%;
  display: block;
  margin: 0 auto;
  width: 300px;
  height: 300px;
  background-color: blanchedalmond;
  padding: 2em;
  border-radius: 1em;
}
.inputs {
  margin-top: 2em;
}
h1, h2 {
  font-weight: normal;
}
a {
  color: #42b983;
}
</style>
