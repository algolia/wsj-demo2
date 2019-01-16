<template>
<div>
  <div class="search-wrapper" v-bind:class="{ active: searchOpen }">
    <div class="search-inner">
      <div class="search-header">
        <div class="search-header-ranking">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="search-header-ranking-icon" v-on:click="toggleRanking()">
            <path d="M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3"></path> <circle cx="12" cy="12" r="10"></circle> <line x1="12" y1="17" x2="12" y2="17"></line>
            </svg>
            <span class="search-header-title" v-on:click="toggleRanking()">View Ranking Info</span>
        </div>
        <div class="search-header-perso">
          <span class="search-header-title" v-on:click="toggleDropdown()">
            <span v-if="activeUser === ''">Choose User Profile</span>
            <span v-if="activeUser != ''">{{activeUser}}</span>
            </span>
        <svg viewBox="0 0 24 24" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="search-header-perso-icon" v-on:click="toggleDropdown()">
          <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path> <circle cx="12" cy="7" r="4"></circle>
          </svg>
          <div class="search-header-perso-users" v-bind:class="{'visible': dropdownIsVisible}">
            <p v-for="user in users" :key="user" v-on:click="selectUser(user)">{{user}}</p>
            <p v-if="activeUser != ''" v-on:click="selectUser('')">Clear current user...</p>
          </div>
          </div>
      </div>
      <div class="search-input">
  <search-input class="search-input" :search-store="searchStore"></search-input>
  <svg class="search-input-icon" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" viewBox="0 0 24 24">
                    <circle cx="10.5" cy="10.5" r="7.5"></circle>
                    <line x1="21" y1="21" x2="15.8" y2="15.8"></line>
                </svg>
    <ais-clear :clears-facets="false" :search-store="searchStore" class="search-input-clear">
                    <template>
                        <svg class="search-input-clear-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <line x1="18" y1="6" x2="6" y2="18"></line>
                            <line x1="6" y1="6" x2="18" y2="18"></line>
                        </svg>
                    </template>
                </ais-clear>
      </div>
      <search-tags :search-store="searchStore" :current-user="activeUser" class="search-tags" />
      <div class="search-refine">
        <ais-menu :search-store="searchStore" attribute="kind" :sort-by="['name:asc']">
        </ais-menu>
        <ais-sort-by-selector :search-store="searchStore" :indices="[
    {
      name: 'dev_WSJ_articles_split2',
      label: 'Most Relevant'
    },
    {
      name: 'dev_WSJ_articles_split2-date',
      label: 'Published Date'
    }
  ]"
/>
      </div>
  <ais-index
    :search-store="searchStore"
    index-name="dev_WSJ_articles_split2"
    :query-parameters="{hitsPerPage: 50, optionalFilters: currentOptionalFilters, sumOrFiltersScores: true, getRankingInfo: true}"
  >
    <ais-results>
      <template scope="{ result }">
        <div class="search-result-wrapper">
          <div class="search-result-inner">
          <div class="search-result-info">
            <div class="search-result-info-cats" v-if="result.categories">
                <span>{{formatSecondaryCat(result.categories.lvl1)}}</span><span> in {{result.categories.lvl0}}</span>
              </div>
              <h3 class="search-result-info-headline headline" v-bind:class="{ paid: result.access === 'paid' }">
                <a :href="result.url">
              <ais-highlight :result="result" attribute-name="title"></ais-highlight>
                </a>
              </h3>
              <div>
                <span class="byline">
                  By {{result.author}}
                </span>
                <span class="date-stamp-container">
                  {{formatDate(result.datePublished)}}
                </span>
              </div>
              <div class="summary-container">
                {{result.description}}
              </div>
                
              <div class="search-result-rank" v-bind:class="{'visible': rankingIsVisible}">
                <div class="search-result-rank-col">
                  <span>Popularity:</span>
                  <span>{{result.popOverTime}}</span>
                </div>
                <div class="search-result-rank-col">
                  <span>Freshness:</span>
                  <span>{{result.freshness}}</span>
                </div>
                <div class="search-result-rank-col">
                  <span>Engagement:</span>
                  <span>{{result.engagementLog}}</span>
                </div>
                <div class="search-result-rank-col">
                  <span>Personalization Score:</span>
                  <span>{{result._rankingInfo.filters}}</span>
                </div>
                </div>
          </div>
          <div class="search-result-img">
            <img :src="result.image">
          </div>
          </div>
          <related-items :result="result" />
        </div>
      </template>
    </ais-results>
  </ais-index>
  </div>
  </div>
<div class="style__search-sector_1_uWmuA4Mb6Rzbr6iRNIT_ style__sector_3A7T20iBI8zxXSk87Vy2iA" data-reactid=".2gfiww9838o.1.1.4.2.2">
<div class="style__search-wrapper_1Uo_2JqNuQyMAb8YwnPgqt" data-reactid=".2gfiww9838o.1.1.4.2.2.0">
  <div class="style__search-button_2cXfJYjestuewRp7BKUqed" data-reactid=".2gfiww9838o.1.1.4.2.2.0.0" v-on:click="toggleSearch">
    <span data-reactid=".2gfiww9838o.1.1.4.2.2.0.0.0" class="search-placeholder">Search</span>
  </div>
   </div>
   </div>
</div>
</template>

<script>
import _ from "lodash";
import { createFromAlgoliaCredentials } from "vue-instantsearch";
import algoliasearch from "algoliasearch";
import SearchInput from "./components/SearchInput.vue";
import SearchTags from "./components/SearchTags.vue";
import RelatedItems from "./components/RelatedItems.vue";

const searchStore = createFromAlgoliaCredentials(
  "latency",
  "af044fb0788d6bb15f807e4420592bc5"
);

const months = [
  "January",
  "February",
  "March",
  "April",
  "May",
  "June",
  "July",
  "August",
  "September",
  "October",
  "November",
  "December"
];

export default {
  data: () => {
    return {
      searchStore,
      searchOpen: false,
      activeUser: "",
      users: ["Political Junkie", "Financial Wiz", "Tech Obsessed"],
      dropdownIsVisible: false,
      rankingIsVisible: false,
      currentOptionalFilters: []
    };
  },
  components: {
    searchInput: SearchInput,
    searchTags: SearchTags,
    relatedItems: RelatedItems
  },
  methods: {
    toggleSearch() {
      this.searchOpen = !this.searchOpen;
    },
    toggleDropdown() {
      this.dropdownIsVisible = !this.dropdownIsVisible;
    },
    toggleRanking() {
      this.rankingIsVisible = !this.rankingIsVisible;
    },
    formatSecondaryCat(cat) {
      return cat ? cat.split(" > ")[1] : "";
    },
    formatDate(timestamp) {
      const date = new Date(timestamp);
      const dateYear = date.getFullYear();
      const dateMonth = date.getMonth();
      const dateDay = date.getDate();
      return months[dateMonth] + " " + dateDay + ", " + dateYear;
    },
    selectUser(user) {
      this.toggleDropdown();
      this.activeUser = user;
      this.buildOptionalFilters();
    },
    buildOptionalFilters() {
      if (this.activeUser != '') {
        if (this.activeUser === 'Political Junkie') {
          this.currentOptionalFilters = ["tags:'presidential election'<score=2>", "tags:elections<score=3>", "tags:trump<score=1>", "tags:clinton<score=1>", "tags:republicans<score=4>", "tags:democrats<score=4>"]
        }
        if (this.activeUser === 'Financial Wiz') {
          this.currentOptionalFilters = ["tags:earnings<score=4>", "tags:'federal reserve'<score=3>", "tags:'earnings season'<score=3>", "tags:'earnings outlook'<score=3>", "tags:revenue<score=1>", "tags:sales<score=1>"]
        }
        if (this.activeUser === 'Tech Obsessed') {
          this.currentOptionalFilters = ["tags:technology<score=4>", "tags:internet<score=4>", "tags:apple<score=3>", "tags:google<score=3>", "tags:iphone<score=2>"]
        }
      }
      else {
        this.currentOptionalFilters = [];
      }
    }
  }
};
</script>
