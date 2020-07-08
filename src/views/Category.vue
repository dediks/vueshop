<template>
  <div>
    <v-card :to="'/category/'+ findCategoryBy.slug" v-if="findCategoryBy.slug">
      <v-img :src="getImage(findCategoryBy.image)" class="white--text">
        <v-card-title class="fill-height align-end" v-text="findCategoryBy.name"></v-card-title>
      </v-img>
    </v-card>

    <v-container class="ma-0 pa-0" grid-list-sm v-if="books">
      <v-subheader>All Books with {{ findCategoryBy.name }} Category</v-subheader>
      <v-layout wrap>
        <v-flex v-for="(book) in findCategoryBy.books" :key="`book-`+book.id" xs6>
          <book-item :book="book" />
        </v-flex>
      </v-layout>
    </v-container>
    <template>
      <div class="text-center">
        <!-- <v-pagination v-model="page" @input="go" :length="lengthPage" :total-visible="5"></v-pagination> -->
      </div>
    </template>
  </div>
</template>
<script>
import { GET_CATEGORY_BY_SLUG } from "../constants/graphql";

export default {
  components: {
    BookItem: () =>
      import(/* webpackChunkName: "book-item" */ "@/components/BookItem.vue")
  },
  data: () => ({
    findCategoryBy: {},
    books: [],
    page: 0,
    lengthPage: 0
  }),
  apollo: {
    findCategoryBy: {
      query: GET_CATEGORY_BY_SLUG,
      variables() {
        return {
          identifier: "slug",
          operator: "=",
          value: this.$route.params.slug
        };
      }
    }
  }
};
</script>
