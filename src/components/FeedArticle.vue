<template>
  <div class="container">
    <small 
      :class="{
        'violet': article.feedColorCode === 0,
        'red': article.feedColorCode === 1,
        'green': article.feedColorCode === 2,
        'orange': article.feedColorCode === 3,
        'blue': article.feedColorCode === 4,
        }"
    >
        {{ loadingUrl === article.sourceUrl ? 'loading...' : article.feed | trimProtocol }}
      </small>
    <div class="corner-mock"></div> 
    <span class="image-wrapper" v-if="article.imageUrl" @click="openContentModal()">
      <img class="image-content" :src="article.imageUrl" alt="Picture not available">
    </span>
    <span v-else-if="article.category" style="height: 34px;"></span>
    <span class="text-pane">
      <h1 id="article-title" @click="openContentModal()">{{ article.title }}</h1>
      <span class="author-and-date">
        <h2>{{ article.author }}</h2>
        <h2>{{ localTime }}</h2>
      </span>
      <p id="article-description" @click="openContentModal()">{{ article.description}}</p>
    </span>
    <div 
      id="category-badge"
      :class="{
        'violet': categoryColorCode === 0,
        'red': categoryColorCode === 1,
        'green': categoryColorCode === 2,
        'orange': categoryColorCode === 3,
        'blue': categoryColorCode === 4,
        }"       
      v-if="article.category">{{ article.category }}</div>
  </div>
</template>

<script>
import moment from 'moment';

export default {
  name: 'FeedArticle',
  props: {
    article: Object,
    loadingUrl: String,
    categories: Array
  },
  methods: {
    openContentModal() {
      this.$emit('openContentModal');
    }
  },
  filters: {
    trimProtocol(value) {
      if (!value) return '';
      return value.split('://').pop();
    }
  },
  computed: {
    categoryColorCode() {
      return this.categories.indexOf(this.article.category)%5;
    },
    localTime() {
      return this.article.pubDate ? moment(this.article.pubDate).local().format('HH:mm LL') : '';
    }
  }
}
</script>

<style scoped>
h1 {
  font-size: 18px;
}

h2 {
  font-size: 14px;
}

small {
  color: rgba(255, 255, 255, 0.575);
  padding: 1px 20px;
  font-size: 16px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

#article-title {
  cursor: pointer;
}

#article-description {
  cursor: pointer;
}

#category-badge {
  position: absolute;
  color: white;
  margin: 37px 16px;
  padding: 5px 9px;
  border-radius: 30px;
}

.container {
  display: flex;
  flex-direction: column;
  width: 500px;
  margin: 20px;
  box-shadow: 3px 3px 5px gray;
  background-color: white;
  overflow: hidden;
}
.image-wrapper {
  max-height: 320px;
  overflow: hidden;
  cursor: pointer;
}

.image-content {
  width: 500px;
}

.text-pane {
  padding: 6px 12px;
}
.author-and-date {
  display: flex;
  justify-content: space-between;
}

.corner-mock {
  position:absolute;
  margin: -27px;
  width: 40px;
  height: 40px;
  background-color: rgb(233, 233, 233);
  transform: rotate(30deg);
}

.violet {
  background-image: linear-gradient(176deg, rgb(130, 55, 149), rgb(0, 65, 90));
}
.orange {
  background-image: linear-gradient(176deg, rgb(155, 96, 28), rgb(180, 180, 62));
}
.green {
  background-image: linear-gradient(176deg, rgb(42, 134, 55), rgb(159, 167, 87));
}
.blue {
  background-image: linear-gradient(176deg, rgb(36, 126, 211), rgb(29, 151, 155));
}
.red {
  background-image: linear-gradient(176deg, rgb(168, 50, 50), rgb(179, 90, 171));
}

@media screen and (max-width: 1100px){
.container {
  width: 400px;
}

.image-content {
  width: 400px;
}
}

@media screen and (max-width: 700px){
.container {
  width: 98vw;
  box-shadow: none;
  margin: 16px 0px;
}

.image-content {
  width: 100vw;
}

.corner-mock {
  display: none;
}

}
</style>
