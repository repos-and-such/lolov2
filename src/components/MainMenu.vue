<template>
  <div class="container">
    <p>
      <b>Add new feed:</b>
      <span class="input-module">
        <input type="text" v-model="addFeedInput" @click="editedFeed = ''" @keydown.enter="addFeed()">
        <button type="button" class="add">
          <i class="material-icons" @click="addFeed()">check</i>
        </button>
      </span>
    </p>
    <p id="my-feeds">
      <b>My feeds:</b>
      <ul v-for="feed in feeds" :key="feed">
        <span class="input-module" v-if="editedFeed === feed">
          <input v-model="editFeedInput" @keydown.enter="confirmEdit(feed)"/>
          <button type="button" class="add">
            <i class="material-icons" @click="confirmEdit(feed)">check</i>
          </button>
        </span>
        <li v-else>
          <span id="my-feed">
            {{ feed }}
          </span>
          <button type="button" class="edit">
            <i class="material-icons" id="edit-icon" @click="openEditField(feed)">create</i>
          </button>
          <button type="button" class="delete">
            <i class="material-icons" id="delete-icon" @click="deleteFeed(feed)">delete_forever</i>
          </button>
        </li>
      </ul>
    </p>
    <p>
      <b v-if="categories.length > 0">Categories:</b>
      <ul v-for="category in categories" :key="category" @click="toggleCategoryFilter(category)">
          <button :class="[categoryFilters.includes(category) ? 'feed-category-selected' : 'feed-category' ]">{{ category }}</button>
      </ul>
    </p>
  </div>
</template>

<script>
export default {
  name: 'MainMenu',
  props: {
    feeds: Array,
    categories: Array,
    categoryFilters: Array
  },
  data() {
    return {
      addFeedInput: '',
      editFeedInput: '',
      editedFeed: ''
    }
  },
  methods: {
    addFeed() {
      let input = this.addFeedInput || this.editFeedInput;
      if (!this.validateInput(input)) return;
      input.trim();
      this.$emit('addFeed', input);
      this.addFeedInput = '';
      this.editFeedInput = '';
    },
    deleteFeed(feed) {
      this.$emit('deleteFeed', feed);
    },
    validateInput(input) {
      return input.includes('http') && (input.includes('.rss') || input.includes('.xml')) && input.length > 10 && input.length < 1000;
    },
    openEditField(feed) {
      this.addFeedInput = '';
      this.editFeedInput = feed;
      this.editedFeed = feed;
    },
    confirmEdit(feed) {
      this.deleteFeed(feed);
      this.addFeed(this.editFeedInput);
      this.editedFeed = '';
    },
    toggleCategoryFilter(category) {
      this.$emit('toggleCategoryFilter', category)
    }
  }
}
</script>

<style scoped>
ul, p {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  padding: 0px;
  margin: 12px;
}

b {
  margin-right: 4px;
}

input {
  width: 60vw;
  height: 24px;
  margin-left: 10px;
  font-family: Montserrat;
  font-size: 16px;
  padding: 3px 16px;
  border-radius:50px;
  resize: none;
  border: 1px solid rgb(184, 184, 184);
  box-shadow: 0 0 4px rgb(211, 210, 210);
  overflow-y: hidden;
  white-space: nowrap;
  overflow: hidden;
}

input:focus {
  outline: none !important;
  border: 1px solid rgb(226, 193, 174);
  box-shadow: 0 0 4px 1px rgb(226, 193, 174);
}

li {
  display: flex;
  align-items: center;
}

button {
  border: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  background: transparent;
  cursor: pointer;
}

button:focus {
  outline: none !important;
}

button:active {
  transform: scale(0.95);
  outline: none !important;
  box-shadow: none;
}

#my-feeds {
  flex-direction: column;
  align-items: flex-start;
}

#edit-icon {
  font-size: 24px;
  color: rgb(93, 93, 172);
}

#delete-icon {
  font-size: 24px;
  color: rgb(172, 93, 93);
}

.container {
  position: sticky;
  top: 50px;
  display: flex;
  flex-direction: column;
  font-size: 20px;
  width: 100vw;
  background-color: white;
  padding: 0px 10vw;
  z-index: 55;
  box-shadow: 0px 2px 2px rgb(187, 185, 185);
}

.material-icons {
  color: rgb(51, 138, 116);
  margin-left: 16px;
}

.feed-category, .feed-category-selected {
  font-family: Montserrat;
  font-size: 20px;
  color: rgb(161, 161, 161);
  cursor: pointer;
}

.feed-category-selected {
  font-weight: bold;
  color: rgb(21, 153, 113);
}

.input-module {
  display: flex;
  align-items: center;
}

@media screen and (max-width: 700px){
input {
  margin-left: 3px;
  width: 66vw;
}

ul {
  margin: 4px 0px;
}

#my-feed {
  max-width: 70vw;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.material-icons {
  margin-left: 0px;
}

.container {
  font-size: 16px;
}

.feed-category, .feed-category-selected {
  font-size: 16px;
}


}
</style>
