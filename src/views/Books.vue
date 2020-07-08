<template>
  <div>
    <v-container class="ma-0 pa-0" grid-list-sm>
      <v-subheader>All Books</v-subheader>
      <v-layout wrap v-if="booksPage">
        <v-flex v-for="(book) in booksPage.books" :key="`book-`+book.id" xs6>
          <book-item :book="book" />
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
import { BOOKS_PAGE_QUERY } from "../constants/graphql";

const pageSize = 4;

export default {
  components: {
    BookItem: () =>
      import(/* webpackChunkName: "book-item" */ "@/components/BookItem.vue")
  },
  data: () => ({
    page: 0,
    showMoreEnabled: true
  }),
  apollo: {
    booksPage: {
      query: BOOKS_PAGE_QUERY,
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
      this.$apollo.queries.booksPage.fetchMore({
        // New variables
        variables: {
          page: this.page,
          pageSize
        },
        // Transform the previous result with new data
        updateQuery: (previousResult, { fetchMoreResult }) => {
          const newBooks = fetchMoreResult.booksPage.books;
          const hasMore = fetchMoreResult.booksPage.hasMore;

          this.showMoreEnabled = hasMore;

          return {
            booksPage: {
              __typename: previousResult.booksPage.__typename,

              books: [...previousResult.booksPage.books, ...newBooks],
              hasMore
            }
          };
        }
      });
    }
  }
};
</script>
