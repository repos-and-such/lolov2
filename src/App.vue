<template>
  <div id="app">
    <div v-for="article in articles" :key="article.id">{{ article }}</div>
    <feed-article />
  </div>
</template>

<script>
import FeedArticle from './components/FeedArticle'

export default {
  name: 'App',
  components: {
    FeedArticle
  },
  data() {
    return {
      rssFeeds: ['https://flipboard.com/@raimoseero/feed-nii8kd0sz.rss'],
      articles: [{title: "example", id: 1}, {title: "bexample", id: 2}]
    }
  },
  methods: {
    fetchData(rssFeeds) {
      const request = new XMLHttpRequest();
      const self = this;

      request.open("GET", rssFeeds[0], true);

      request.onreadystatechange = function () {
        if (request.readyState == 4 && request.status == 200) {
          self.populateObjectsArray(request.responseXML.documentElement, rssFeeds[0]);
        }
      };
      request.send(null);
    },
    populateObjectsArray(xmlContent, feed) {
      const itemList = xmlContent.getElementsByTagName('item');

      itemList.forEach(item => {
        let article = {};

        article.title = item.getElementsByTagName('title').item(0) ? item.getElementsByTagName('title').item(0).textContent : '';
        article.category = item.getElementsByTagName('category').item(0) ? item.getElementsByTagName('category')[0].textContent : '';
        article.pubDate = item.getElementsByTagName('pubDate').item(0) ? item.getElementsByTagName('pubDate')[0].textContent : '';
        article.author = item.getElementsByTagName('author').item(0) ? item.getElementsByTagName('author')[0].textContent : '';
        article.description = item.getElementsByTagName('description').item(0) ? item.getElementsByTagName('description')[0].textContent : '';
        article.sourceUrl = item.getElementsByTagName('link').item(0) ? item.getElementsByTagName('link')[0].textContent : '';
        article.feed = feed;

        this.articles.push(article);
      }); 
    }
  },

  mounted() {
    this.fetchData(this.rssFeeds);
  }
}
</script>

<style>
</style>
