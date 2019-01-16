<template>
<div class="search-result-rel">
    <span class="search-result-rel-link" v-on:click="toggleVisibility">
                  <span v-if="!isVisible">+</span><span v-if="isVisible">-  </span> Explore Similar Articles 
                </span>
                <ais-index v-if="isVisible" :search-store="searchStore" index-name="dev_WSJ_articles_split2" :query="queryInt" :query-parameters="{filters: filtersList, hitsPerPage: 3, restrictSearchableAttributes: ['tags'], removeWordsIfNoResults: 'allOptional'}">
                    <ais-results class="search-result-rel-wrapper">
                        <template scope="{ result }">
                            <div class="search-result-wrapper">
          <div class="search-result-info">
            <div class="search-result-info-cats" v-if="result.categories">
                <span>{{formatSecondaryCat(result.categories.lvl1)}}</span><span> in {{result.categories.lvl0}}</span>
              </div>
              <h3 class="search-result-info-headline headline" v-bind:class="{ paid: result.access === 'paid' }">
                <a :href="result.url">
              {{result.title}}
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
          </div>
        </div>
                        </template>
                    </ais-results>
                </ais-index>
</div>
</template>

<script>
import { createFromAlgoliaCredentials } from "vue-instantsearch";
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
  props: ["result"],
  data: () => {
    return {
      searchStore,
      isVisible: false
    };
  },
  computed: {
    queryInt() {
      return this.result.tags.join(" ");
    },
    filtersList() {
      return "NOT articleID:" + this.result.articleID;
    }
  },
  methods: {
    toggleVisibility() {
      this.isVisible = !this.isVisible;
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
    }
  }
};
</script>
