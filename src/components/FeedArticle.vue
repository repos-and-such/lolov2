<template>
  <div class="article">
    <small>{{ article.feed }}</small> 
    <span class="image" v-if="article.imageUrl">
      <img :src="article.imageUrl" width="400px" alt="Picture not available">
    </span>
    <span class="text-pane">
      <h1>{{ article.title }}</h1>
      <span class="author-and-date">
        <h2>{{ article.author }}</h2>
        <h2>{{ article.pubDate }}</h2>
      </span>
      <p>{{ article.description}}</p>
    </span>
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

small {
  background-color: purple;
  color: white;
}
.article {
  display: flex;
  flex-direction: column;
  width: 400px;
  margin: 20px;
  box-shadow: 0px 0px 5px gray;
}
.article {
  background-color: white;
}
.image {
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

</style>
