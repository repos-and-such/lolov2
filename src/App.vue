<template>
  <div id="app">
    <div v-for="article in sortedArticles" :key="article.id">
      <feed-article :article="article"/>
    </div>
  </div>
</template>

<script>
import FeedArticle from './components/FeedArticle';
import { v4 as uuidv4 } from 'uuid';

export default {
  name: 'App',
  components: {
    FeedArticle
  },
  data() {
    return {
      rssFeeds: ['https://flipboard.com/@raimoseero/feed-nii8kd0sz.rss', 'https://www.nasa.gov/rss/dyn/breaking_news.rss'],
      articles: []
    }
  },
  methods: {
    fetchData(rssFeeds) {
      rssFeeds.forEach(feed => {
        this.fetchFeedArticles(feed)
      });
    },
    fetchFeedArticles(feed) {
      const request = new XMLHttpRequest();
      const self = this;

      request.open("GET", feed, true);

      request.onreadystatechange = function () {
        if (request.readyState == 4 && request.status == 200) {
          self.populateObjectsArray(request.responseXML.documentElement, feed);
        }
      };
      request.send(null);
    },
    populateObjectsArray(xmlContent, feed) {
      const itemList = xmlContent.getElementsByTagName('item');

      for (const item of itemList) {
        let article = {};

        article.title = item.getElementsByTagName('title').item(0) ? item.getElementsByTagName('title').item(0).textContent : '';
        article.category = item.getElementsByTagName('category').item(0) ? item.getElementsByTagName('category')[0].textContent : '';
        article.pubDate = item.getElementsByTagName('pubDate').item(0) ? item.getElementsByTagName('pubDate')[0].textContent : '';
        article.author = item.getElementsByTagName('author').item(0) ? item.getElementsByTagName('author')[0].textContent : '';
        article.description = item.getElementsByTagName('description').item(0) ? item.getElementsByTagName('description')[0].textContent : '';
        article.sourceUrl = item.getElementsByTagName('link').item(0) ? item.getElementsByTagName('link')[0].textContent : '';
        article.feed = this.formatFeed(feed);
        article.imageUrl = this.findImageUrl(item.innerHTML) ? this.findImageUrl(item.innerHTML) : '';
        article.id = uuidv4();
        this.articles.push(article);
      }
    },
    findImageUrl(innerHtml) {
      let innerHtmlArray = innerHtml.split('"');
      let imageUrl = innerHtmlArray.find(fragment => fragment.includes('.jpg') || fragment.includes('.png') || fragment.includes('.gif'))
      return imageUrl;
    },
    formatFeed(feed) {
      if (feed.includes('https://')) {
        return feed.split('https://').pop();
      } else {
        return feed.split('http://').pop();
      }
    },
    compareDates(a,b) {
      let dateA = new Date(a.pubDate);
      let dateB = new Date(b.pubDate);

      let comparison = 0;

      if (dateA > dateB) {
        comparison = -1;
      } else if (dateA < dateB) {
        comparison = 1;
      }
      return comparison;
    }
  },
  computed: {
    sortedArticles() {
      return this.articles.slice().sort(this.compareDates);
    }
  },
  mounted() {
    this.fetchData(this.rssFeeds);
  }
}
</script>

<style>
#app {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  background-color: rgb(233, 233, 233);
}
</style>
