<template>
  <div class="article">
    <small>{{ article.feed }}</small>
    <div class="corner-mock"></div> 
    <span class="image-wrapper" v-if="article.imageUrl" @click="openContentModal()">
      <img class="image-content" :src="article.imageUrl" alt="Picture not available">
    </span>
    <span v-else-if="article.category" style="height: 34px;"></span>
    
    <div>{{article.sourceUrl}}</div>
    <span class="text-pane">
      <h1 id="article-title" @click="openContentModal()">{{ article.title }}</h1>
      <span class="author-and-date">
        <h2>{{ article.author }}</h2>
        <h2>{{ article.pubDate }}</h2>
      </span>
      <p id="article-description" @click="openContentModal()">{{ article.description}}</p>
    </span>
    <div class="category-badge" v-if="article.category">{{ article.category }}</div>
  </div>
</template>

<script>
export default {
  name: 'FeedArticle',
  props: {
    article: Object
  },
  methods: {
    openContentModal() {
      this.$emit('openContentModal');
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
  background-color: rgb(0, 98, 128);
  color: rgba(255, 255, 255, 0.575);
  padding: 1px 16px;
}

#article-title {
  cursor: pointer;
}

#article-description {
  cursor: pointer;
}

.article {
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

.category-badge {
  position: absolute;
  background: rgb(131, 149, 80);
  color: white;
  margin: 32px 18px;
  padding: 5px 9px;
  border-radius: 30px;
  box-shadow: 0px 0px 3px rgb(131, 149, 80);
}

.corner-mock {
  position:absolute;
  margin: -28px;
  width: 40px;
  height: 40px;
  background-color: rgb(233, 233, 233);
  transform: rotate(30deg);
}

@media screen and (max-width: 800px){
.article {
  width: 100vw;
  box-shadow: none;
  margin: 0px;
}

.image-content {
  width: 100vw;
}

.corner-mock {
  display: none;
}
}
</style>
