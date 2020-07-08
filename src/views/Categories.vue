<template>
  <div>
    <v-container class="ma-0 pa-0" grid-list-sm>
      <v-subheader>All Category</v-subheader>
      <v-layout wrap v-if="categoriesPage">
        <v-flex v-for="(category) in categoriesPage.categories" :key="`category-`+category.id" xs6>
          <v-card :to="'/category/'+ category.slug">
            <v-img :src="getImage(category.image)" class="white--text">
              <v-card-title class="fill-height align-end" v-text="category.name"></v-card-title>
            </v-img>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
    <template>
      <div class="text-center" v-if="!$apollo.loading">
        <button v-if="showMoreEnabled" @click="showMore">Show more</button>
      </div>
    </template>
  </div>
</template>
<script>
import { CATEGORIES_PAGE_QUERY } from "../constants/graphql";

const pageSize = 4;

export default {
  data: () => ({
    page: 0,
    showMoreEnabled: true
  }),
  apollo: {
    categoriesPage: {
      query: CATEGORIES_PAGE_QUERY,
      variables() {
        return {
          page: 0,
          size: pageSize
        };
      }
    }
  },
  methods: {
    showMore() {
      this.page++;
      // Fetch more data and transform the original result
      this.$apollo.queries.categoriesPage.fetchMore({
        // New variables
        variables: {
          page: this.page,
          pageSize
        },
        // Transform the previous result with new data
        updateQuery: (previousResult, { fetchMoreResult }) => {
          const newCategories = fetchMoreResult.categoriesPage.categories;
          const hasMore = fetchMoreResult.categoriesPage.hasMore;

          this.showMoreEnabled = hasMore;

          return {
            categoriesPage: {
              __typename: previousResult.categoriesPage.__typename,

              categories: [
                ...previousResult.categoriesPage.categories,
                ...newCategories
              ],
              hasMore
            }
          };
        }
      });
    }
  }
};
</script>
