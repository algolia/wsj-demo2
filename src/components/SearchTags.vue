<template>
    <div>
        <div class="search-tags-container" v-if="!this.searchStore.query">
            <span class="active" v-for="tag in filtersList" :key="tag" v-on:click="removeTag(tag)">
                {{tag}}
            </span>
            <span v-for="tag in searchTags" :key="tag" v-on:click="addTag(tag)">
                {{tag}}
            </span>
        </div>
        <div class="search-tags-container" v-if="this.searchStore.query">
            <span v-for="facet in facetValues" :key="facet.name" v-on:click="addTag(facet.name)">
                {{facet.name}}
            </span>
        </div>
    </div>
</template>

<script>
import { Component } from "vue-instantsearch";

export default {
  mixins: [Component],
  props: ["currentUser"],
  data: () => {
    return {
      searchTags: [],
      index: {},
      attrName: "tags",
      filtersList: []
    };
  },
  watch: {
    currentUser: function() {
      this.filtersList = [];
      this.searchForFacetValues();
    }
  },
  computed: {
    query() {
      return this.searchStore.query;
    },
    facetValues() {
      return this.searchStore.getFacetValues(
        this.attrName,
        ["count:desc", "name:asc"],
        10
      );
    }
  },
  methods: {
    searchForFacetValues() {
      if (this.currentUser != "") {
        if (this.currentUser === "Political Junkie") {
          if (!this.filtersList.includes("elections")) {
            this.filtersList.push("elections");
          }
        }
        if (this.currentUser === "Financial Wiz") {
          if (!this.filtersList.includes("earnings")) {
            this.filtersList.push("earnings");
          }
        }
        if (this.currentUser === "Tech Obsessed") {
          if (!this.filtersList.includes("technology")) {
            this.filtersList.push("technology");
          }
        }
      }
      this.index.searchForFacetValues(
        {
          facetName: this.attrName,
          facetQuery: "",
          filters: this.formatFilters(this.filtersList)
        },
        (err, content) => {
          if (err) {
            console.error(err);
            return;
          }
          var filtersList = this.filtersList;
          var nonFilteredSearchTags = content.facetHits.map(hit => hit.value);
          this.searchTags = nonFilteredSearchTags.filter(tag => (this.filtersList.includes(tag) ? false : true));
        }
      );
    },
    addTag(tag) {
      this.filtersList = [...this.filtersList, tag];
      this.searchForFacetValues();
      this.toggleRefinement(tag);
      this.searchStore.query = "";
    },
    removeTag(tag) {
      this.filtersList = _.without(this.filtersList, tag);
      this.searchForFacetValues();
      this.toggleRefinement(tag);
    },
    formatFilters() {
      return this.filtersList.length === 0
        ? ""
        : this.filtersList
            .map((tag, index) => {
              return index === 0
                ? this.attrName + ':"' + tag + '"'
                : "AND " + this.attrName + ':"' + tag + '"';
            })
            .join(" ");
    },
    toggleRefinement(tag) {
      return this.searchStore.toggleFacetRefinement(this.attrName, tag);
    }
  },
  created() {
    this.searchStore.addFacet(this.attrName);
    this.index = this.searchStore.algoliaClient.initIndex(
      "dev_WSJ_articles_split2"
    );
    this.searchForFacetValues();
  }
};
</script>

