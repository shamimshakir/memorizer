<template>
  <main>
    <h4>All Words</h4>
    <div class="my-3 d-flex justify-content-between align-items-end">
      <div>
        <div class="btn-group" role="group" aria-label="Basic checkbox toggle button group">
          <input
            type="checkbox"
            class="btn-check"
            id="english"
            autocomplete="off"
            value="english"
            v-model="colOnOff"
          />
          <label class="btn btn-sm btn-outline-primary" for="english">English</label>
          <input
            type="checkbox"
            class="btn-check"
            id="bangla"
            autocomplete="off"
            value="bangla"
            v-model="colOnOff"
          />
          <label class="btn btn-sm btn-outline-primary" for="bangla">Bangla</label>
        </div>
      </div>
      <div>
          <input type="date" id="date_data" class="form-control" v-model="date_data">
      </div>
      <div>
        <button type="button" @click="shuffle()" class="btn btn-success btn-sm me-2">Randomize Table</button>
        <button
          type="button"
          class="btn btn-success btn-sm"
          data-bs-toggle="modal"
          data-bs-target="#exampleModal"
        >Add Word</button>
      </div>
    </div>
    <table class="table table-dark table-bordered table-striped rounded overflow-hidden table-sm">
      <thead>
        <tr>
          <th width="60">SL</th>
          <th v-if="colOnOff.includes('english')">English</th>
          <th v-if="colOnOff.includes('bangla')">Bangla</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(word, index) in words" :key="word.id">
          <td>{{ index + 1 }}</td>
          <td v-if="colOnOff.includes('english')">{{ word.en_word }}</td>
          <td v-if="colOnOff.includes('bangla')">{{ word.bn_meaning }}</td>
        </tr>
      </tbody>
    </table>

    <!-- Modal -->
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <form @submit.prevent="addWord($event)">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Add Word</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <div class="form-group">
                <label class="form-label">English Word</label>
                <input type="text" class="form-control" v-model="wordForm.en_word" />
              </div>
              <div class="form-group">
                <label class="form-label">Bangla Meaning</label>
                <input type="text" class="form-control" v-model="wordForm.bn_meaning" />
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" ref="Close" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
              <button type="submit" class="btn btn-primary">Save Word</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      words: [],
      mainWords: [],
      colOnOff: ["english", "bangla"],
      wordForm: {},
      date_data: null
    }
  },
  watch: {
    date_data(value) {
      let wordsByDate = this.mainWords.filter(word => {
        return this.isSameDay(new Date(word.created_at), new Date(value));
      });
      this.words = wordsByDate;
    }
  },
  methods: {
    shuffle() {
      let array = this.words;
      let currentIndex = array.length,  randomIndex;
      while (currentIndex != 0) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;
        [array[currentIndex], array[randomIndex]] = [
          array[randomIndex], array[currentIndex]];
      }
      this.words = array;
    },
    getWords() {
      this.axios.get('https://nextech.readyeshop.com/api/vocs')
        .then(res => {
          this.words = res.data.data;
          this.mainWords = res.data.data;
        })
        .catch(function (error) {
          console.dir(error);
        });

    },
    isSameDay(d1, d2) {
      return d1.getFullYear() === d2.getFullYear() &&
        d1.getDate() === d2.getDate() &&
        d1.getMonth() === d2.getMonth();
    },
    addWord() {
      this.axios.post('https://nextech.readyeshop.com/api/vocs',this.wordForm)
        .then(response => {
          alert(response.data.message);
          this.$refs.Close.click();
          this.getWords();
          this.wordForm = {};
        })
        .catch(function (error) {
          console.dir(error);
        });
    },
  },
  mounted() {
    this.getWords()
  }
}
</script>
