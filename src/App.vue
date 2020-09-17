<template>
  <div id="app">
    <header class="error-panel" v-if="errorOpen">
      Oh snap, an error :(
    </header>
    <header v-else>
      <b>Lolo v2</b>
      <i class="material-icons" @click="toggleMenu()">menu</i>
    </header>
    <main-menu 
      v-if="menuOpen"
      @addFeed="addFeed"
      @deleteFeed="deleteFeed"
      @toggleCategoryFilter="toggleCategoryFilter"
      :feeds="rssFeeds"
      :categories="availableCategories"
      :categoryFilters="categoryFilters"
      :lastAddedFeed="lastAddedFeed"
    />
    <content-modal 
      v-if="modalOpen"
      :content="openArticleContent"
      @close="closeContent()"
    />
    <div class="articles-container">
      <h3 v-if="articles.length === 0">Add some feeds to stay informed of the latest trends!</h3>
      <ul v-for="article in filteredArticles" :key="article.id">
        <feed-article 
          :article="article"
          :loadingUrl="loadingUrl"
          :categories="availableCategories"
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
      rssFeeds: [],
      articles: [],
      modalOpen: false,
      openArticleContent: '',
      loadingUrl: null,
      proxy: 'https://cors-anywhere.herokuapp.com/',
      menuOpen: false,
      errorOpen: false,
      availableCategories: [],
      categoryFilters: [],
      lastAddedFeed: ''
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
      
      request.open("GET",
      this.proxy + 
      feed, true);

      request.onreadystatechange = function () {
        if (request.readyState == 4 && request.status == 200) {
          if (request.responseXML) {
            if (!self.rssFeeds.includes(feed)) {
              self.rssFeeds.push(feed);
              setTimeout(() => {
                self.lastAddedFeed = feed;
              }, 2000);
              localStorage.setItem('storedFeeds', self.rssFeeds);
            }
            self.populateObjectsArray(request.responseXML.documentElement, feed);
          } else {
            self.displayError();
            console.error(`Feed ${feed} didn't respond`);
          }
        } else if (request.readyState == 4 && request.status !== 200) {
            self.displayError();
            console.error(`Feed ${feed} responded with error`);
          }
      };
      request.send(null);
    },
    fetchArticleContent(url) {
      this.loadingUrl = url;
      Mercury.parse(
        this.proxy + 
        url)
          .then(result => {
            if (result.error) {
              this.displayError();
              this.loadingUrl = null;
            } else {
              let content = result.content;
              this.openContentModal(content);
              this.loadingUrl = null;
            }
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
        article.feed = feed;
        article.feedColorCode = this.rssFeeds.indexOf(feed)%5;
        article.imageUrl = this.findImageUrl(item.innerHTML) ? this.findImageUrl(item.innerHTML) : '';
        article.id = uuidv4();
        
        if (!this.articles.find(a => a.sourceUrl === article.sourceUrl)) {
          this.articles.push(article);
        }
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
    deleteFeed(feed) {
      let index = this.rssFeeds.indexOf(feed);

      if (index > -1) {
        this.rssFeeds.splice(index, 1);
        localStorage.setItem('storedFeeds', this.rssFeeds);
        this.removeFeedsArticles(feed);
      } else {
        this.displayError();
        console.error(`Feed ${feed} doesn't exist`);
      }
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
        if (index > -1) {
          this.categoryFilters.splice(index, 1);
        } else {
          this.displayError();
          console.error(`Category ${category} doesn't exist`);
        }
      } else {
        this.categoryFilters.push(category);
      }
    },
    removeFeedsArticles(feed) {
    
      for (let i = this.articles.length - 1; i > -1; i--) {
        let article = this.articles[i];
        if (article.feed === feed) {
          this.articles.splice(i, 1);
        }
      }
    },
    initializeLocalStorage() {
      if (!localStorage.getItem('storedFeeds')) {
        localStorage.setItem('storedFeeds', `https://flipboard.com/@raimoseero/feed-nii8kd0sz.rss`)
      } 
      // else if (!localStorage.getItem('storedFeeds').includes('https://flipboard.com/@raimoseero/feed-nii8kd0sz.rss')) {
      //   localStorage.setItem('storedFeeds', `${localStorage.getItem('storedFeeds')},https://flipboard.com/@raimoseero/feed-nii8kd0sz.rss`)
      // }
      this.rssFeeds = localStorage.getItem('storedFeeds').split(',');
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
    this.initializeLocalStorage();
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

h3 {
  color: gray;
  margin: 10px;
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
  height: 100%;
  background-color: rgb(233, 233, 233);
}


@media screen and (max-width: 700px){
i {
  margin-left: 40vw;
}
}
</style>
