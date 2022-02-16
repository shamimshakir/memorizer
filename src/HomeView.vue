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
          <input
            type="checkbox"
            class="btn-check"
            id="synonym"
            autocomplete="off"
            value="synonym"
            v-model="colOnOff"
          />
          <label class="btn btn-sm btn-outline-primary" for="synonym">Synonym</label>
          <input
            type="checkbox"
            class="btn-check"
            id="antonym"
            autocomplete="off"
            value="antonym"
            v-model="colOnOff"
          />
          <label class="btn btn-sm btn-outline-primary" for="antonym">Antonym</label>
        </div>
      </div>
      <div>
          <input type="date" id="date_data" class="form-control" v-model="date_data">
          {{ date_data }}
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
    <table class="table table-dark table-bordered rounded overflow-hidden">
      <thead>
        <tr>
          <th>SL</th>
          <th v-if="colOnOff.includes('english')">English</th>
          <th v-if="colOnOff.includes('bangla')">Bangla</th>
          <th v-if="colOnOff.includes('synonym')">Synonym</th>
          <th v-if="colOnOff.includes('antonym')">Antonym</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(word, index) in words" :key="word.id">
          <td>{{ index + 1 }}</td>
          <td v-if="colOnOff.includes('english')">{{ word.en_word }}</td>
          <td v-if="colOnOff.includes('bangla')">{{ word.bn_meaning }}</td>
          <td v-if="colOnOff.includes('synonym')">{{ word.synonym }}</td>
          <td v-if="colOnOff.includes('antonym')">{{ word.antonym }}</td>
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
              <div class="form-group">
                <label class="form-label">Synonyms</label>
                <input type="text" class="form-control" v-model="wordForm.synonym" />
              </div>
              <div class="form-group">
                <label class="form-label">Antonyms</label>
                <input type="text" class="form-control" v-model="wordForm.antonym" />
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
      colOnOff: ["english", "bangla", "synonym", "antonym"],
      wordForm: {},
      date_data: null
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
        })
        .catch(function (error) {
          console.dir(error);
        });

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
    // methods can be called in lifecycle hooks, or other methods!
    this.getWords()
  }
}
</script>
