<template>
  <main>
    <p v-for="word in words" :key="word.id">{{ word.en_word }} = {{ word.bn_meaning }}</p>
  </main>
</template>

<script>
export default {
  data() {
    return {
      words: []
    }
  },
  methods: {
    getWords() {
      var config = {
        method: 'get',
        url: 'https://nextech.readyeshop.com/api/vocs',
        headers: { }
      };
      this.axios(config)
      .then(res => {
        this.words = res.data.data;
      })
      .catch(function (error) {
        console.dir(error);
      });

    },
    addWord(){
      var qs = require('qs');
      var data = qs.stringify({});
      var config = {
        method: 'post',
        url: '',
        headers: { },
        data : data
      };

      axios(config)
      .then(function (response) {
        console.dir(response);
      })
      .catch(function (error) {
        console.dir(error);
      });

    }
  },
  mounted() {
    // methods can be called in lifecycle hooks, or other methods!
    this.getWords()
  }
}
</script>
