<template>
  <div id="app">
    <content-modal 
      v-if="modalOpen"
      :content="openArticleContent"
      @close="closeContent()"
    />
    <div v-for="article in sortedArticles" :key="article.id">
      <feed-article 
        :article="article"
        @openContentModal="fetchArticleContent(article.sourceUrl)" 
      />
    </div>
  </div>
</template>

<script>
import FeedArticle from './components/FeedArticle';
import ContentModal from './components/ContentModal';
import Mercury from "@postlight/mercury-parser";

import { v4 as uuidv4 } from 'uuid';

export default {
  name: 'App',
  components: {
    FeedArticle, ContentModal
  },
  data() {
    return {
      rssFeeds: ['https://flipboard.com/@raimoseero/feed-nii8kd0sz.rss', 'https://www.nasa.gov/rss/dyn/breaking_news.rss', 'http://rss.cnn.com/rss/edition.rss', 'https://www.feedforall.com/sample-feed.xml'],
      articles: [],
      modalOpen: false,
      openArticleContent: ''
    }
  },
  methods: {
    fetchAllArticles(rssFeeds) {
      rssFeeds.forEach(feed => {
        this.fetchFeedArticles(feed)
      });
    },
    fetchFeedArticles(feed) {
      const request = new XMLHttpRequest();
      const self = this;
      const proxy = 'https://cors-anywhere.herokuapp.com/'
      request.open("GET", proxy + feed, true);

      request.onreadystatechange = function () {
        if (request.readyState == 4 && request.status == 200) {
          self.populateObjectsArray(request.responseXML.documentElement, feed);
        }
      };
      request.send(null);
    },
    fetchArticleContent(url) {
      const proxy = 'https://cors-anywhere.herokuapp.com/';
      Mercury.parse(proxy + url).then(result => {
        let content = result.content;
        this.openContentModal(content);
      });
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
    },
    openContentModal(content) {
      this.openArticleContent = content;
      this.modalOpen = true;
    },
    closeContent() {
      this.modalOpen = false;
    }
  },
  computed: {
    sortedArticles() {
      return this.articles.slice().sort(this.compareDates);
    }
  },
  mounted() {
    this.fetchAllArticles(this.rssFeeds);
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
