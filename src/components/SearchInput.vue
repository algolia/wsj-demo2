<template>
    <input type="text"
         autocorrect="off"
         autocapitalize="off"
         autocomplete="off"
         spellcheck="false"
         v-model="query"
         placeholder="Search for article titles, categories, authors, or content."
  >
</template>

<script>
import { Component } from 'vue-instantsearch';
export default {
    mixins: [Component],
    computed: {
    query: {
      get() {
        return this.searchStore.query;
      },
      set(value) {
        this.searchStore.stop();
        this.searchStore.query = value;
        this.$emit('query', value);
        // We here ensure we give the time to listeners to alter the store's state
        // without triggering in between ghost queries.
        this.$nextTick(function() {
          this.searchStore.start();
          this.searchStore.refresh();
        });
      },
    },
  },
};
</script>