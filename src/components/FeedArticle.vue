<template>
  <div class="article">
    <small>{{ article.feed }}</small> 
    <span class="image-wrapper" v-if="article.imageUrl">
      <img class="image-content" :src="article.imageUrl" alt="Picture not available">
    </span>
    <span v-else-if="article.category" style="height: 34px;"></span>
    <span class="text-pane">
      <h1>{{ article.title }}</h1>
      <span class="author-and-date">
        <h2>{{ article.author }}</h2>
        <h2>{{ article.pubDate }}</h2>
      </span>
      <p>{{ article.description}}</p>
    </span>
    <div class="category-badge" v-if="article.category">{{ article.category }}</div>
  </div>
</template>

<script>
export default {
  name: 'FeedArticle',
  props: {
    article: Object
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

.image-content {
  width: 500px;
}

small {
  background-color: purple;
  color: rgba(255, 255, 255, 0.575);
  padding: 1px 3px;
}
.article {
  display: flex;
  flex-direction: column;
  width: 500px;
  margin: 20px;
  box-shadow: 0px 0px 5px gray;
  background-color: white;
  overflow: hidden;
}
.image-wrapper {
  max-height: 300px;
  overflow: hidden;
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

@media screen and (max-width: 800px){
.article {
  width: 100vw;
}

.image-content {
  width: 100vw;
}
}
</style>
