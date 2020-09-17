<template>
  <div class="container">
    <p>
      <b>Add new feed:</b> 
      <input type="text" v-model="feedInput" @keydown.enter="addFeed()">
      <i class="material-icons" @click="addFeed()">check</i>
    </p>
    <ul id="my-feeds"><b>My feeds:</b>
      <li v-for="feed in feeds" :key="feed">
        {{ feed }}
        <i class="material-icons" id="edit-icon">create</i>
        <i class="material-icons" id="delete-icon">delete_forever</i>
      </li>
    </ul>
    <ul>
      <b>Categories:</b>
      <li v-for="category in categories" :key="category" @click="toggleCategoryFilter(category)">
        <span :class="[categoryFilters.includes(category) ? 'feed-category-selected' : 'feed-category' ]">{{ category }}</span>
      </li>
    </ul>
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
      feedInput: ''
    }
  },
  methods: {
    addFeed() {
      this.$emit('addFeed', this.feedInput);
      this.feedInput = '';
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
  align-items: center;
  padding: 0px;
  margin: 12px;
}

b {
  margin-right: 4px;
}

input {
  width: 30vw;
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
  margin: 6px;
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
  cursor: pointer;
  margin-left: 16px;
}

.feed-category, .feed-category-selected {
  color: rgb(161, 161, 161);
  cursor: pointer;
}

.feed-category-selected {
  font-weight: bold;
  color: rgb(21, 153, 113);
}

</style>
