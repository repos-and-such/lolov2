<template>
  <div id="app">
    <header class="error-panel" v-if="errorOpen">
      Oh snap, something went wrong...
    </header>
    <header v-else>
      <b>Lolo v2</b>
      <i class="material-icons" @click="toggleMenu()">menu</i>
    </header>
    <main-menu 
      v-if="menuOpen"
      @addFeed="addFeed"
      @toggleCategoryFilter="toggleCategoryFilter"
      :feeds="rssFeeds"
      :categories="availableCategories"
      :categoryFilters="categoryFilters"
    />
    <content-modal 
      v-if="modalOpen"
      :content="openArticleContent"
      @close="closeContent()"
    />
    <div class="articles-container">
      <ul v-for="article in filteredArticles" :key="article.id">
        <feed-article 
          :article="article"
          :loadingUrl="loadingUrl"
          @openContentModal="fetchArticleContent(article.sourceUrl)" 
        />
      </ul>
    </div>
  </div>
</template>

<script>
import FeedArticle from './components/FeedArticle';
import ContentModal from './components/ContentModal';
import MainMenu from './components/MainMenu';
import Mercury from "@postlight/mercury-parser";

import { v4 as uuidv4 } from 'uuid';

export default {
  name: 'App',
  components: {
    FeedArticle, ContentModal, MainMenu
  },
  data() {
    return {
      rssFeeds: [
        'https://flipboard.com/@raimoseero/feed-nii8kd0sz.rss', 
        // 'https://www.nasa.gov/rss/dyn/breaking_news.rss', 
        // 'http://feeds.bbci.co.uk/news/world/rss.xml',
        'https://www.feedforall.com/sample-feed.xml'],
      articles: [],
      modalOpen: false,
      openArticleContent: '',
      loadingUrl: null,
      proxy: 'https://cors-anywhere.herokuapp.com/',
      menuOpen: false,
      errorOpen: false,
      availableCategories: [],
      categoryFilters: []
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
      request.open("GET", this.proxy + feed, true);

      request.onreadystatechange = function () {
        if (request.readyState == 4 && request.status == 200) {
          if (request.responseXML) {
            if (!self.rssFeeds.includes(feed)) {
              self.rssFeeds.push(feed);
            }
            self.populateObjectsArray(request.responseXML.documentElement, feed);
          } else {
            self.displayError();
          }
        }
      };
      request.send(null);
    },
    fetchArticleContent(url) {
      this.loadingUrl = url;
      Mercury.parse(this.proxy + url).then(result => {
        let content = result.content;
        this.openContentModal(content);
        this.loadingUrl = null;
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

        if (article.category && !this.availableCategories.includes(article.category)) {
          this.availableCategories.push(article.category);
        }
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
    },
    toggleMenu() {
      this.menuOpen = !this.menuOpen;
    },
    addFeed(feedUrl) {
      this.fetchFeedArticles(feedUrl);
    },
    displayError() {
      this.errorOpen = true;
      setTimeout(() => {
        this.errorOpen = false;
      }, 2000);
    },
    toggleCategoryFilter(category) {
      if (this.categoryFilters.includes(category)) {
        let index = this.categoryFilters.indexOf(category);
        this.categoryFilters.splice(index, 1);
      } else {
        this.categoryFilters.push(category);
      }
    }
  },
  computed: {
    sortedArticles() {
      return this.articles.slice().sort(this.compareDates);
    },
    filteredArticles() {
      if (this.categoryFilters.length === 0) {
        return this.sortedArticles
      } else {
        return this.sortedArticles.filter(article => this.categoryFilters.includes(article.category));
      }
    }
  },
  mounted() {
    this.fetchAllArticles(this.rssFeeds);
  }
}
</script>

<style>
header {
  position: fixed;
  display: flex;
  justify-content: space-around;
  align-items: center;
  width: 100vw;
  font-size: 28px;
  padding: 10px;
  background-image: linear-gradient(176deg, rgb(72, 0, 90), rgb(0, 87, 90));
  color: white;
  box-shadow: 0px 0px 3px gray;
  z-index: 54;
}

ul {
  padding: 0px;
  margin: 0px;
}

.articles-container {
  margin-top: 60px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.material-icons {
  font-size: 32px;
  cursor: pointer;
}

.error-panel {
  font-weight: bold;
  color: rgb(223, 83, 125);
}

#app {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  background-color: rgb(233, 233, 233);
}
</style>
