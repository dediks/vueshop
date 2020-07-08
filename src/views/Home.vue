<template>
  <div>
    <v-container class="ma-0 pa-0" grid-list-sm>
      <div class="text-right">
        <v-btn small text to="/categories" class="blue--text">
          All Categories
          <v-icon>mdi-chevron-right</v-icon>
        </v-btn>
      </div>
      <v-layout wrap>
        <v-flex v-for="(category) in randomCategory" :key="`category-`+category.id" xs6>
          <v-card :to="'/category/'+ category.slug">
            <v-img :src="getImage(category.image)" class="white--text">
              <v-card-title class="fill-height align-end" v-text="category.name"></v-card-title>
            </v-img>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>

    <v-container class="ma-0 pa-0 mt-2" grid-list-sm>
      <div class="text-right">
        <v-btn small text to="/books" class="blue--text">
          All Books
          <v-icon>mdi-chevron-right</v-icon>
        </v-btn>
      </div>
      <v-layout wrap>
        <v-flex v-for="(book) in books.data" :key="`book-`+book.id" xs6>
          <book-item :book="book" />
        </v-flex>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
import {
  FIRST_TEN_BOOKS_QUERY,
  RANDOMIZED_CATEGORY
} from "../constants/graphql";

export default {
  components: {
    BookItem: () =>
      import(/* webpackChunkName: "book-item" */ "@/components/BookItem.vue")
  },
  apollo: {
    books: {
      query: FIRST_TEN_BOOKS_QUERY
    },
    randomCategory: {
      query: RANDOMIZED_CATEGORY
    }
  },
  data() {
    return {
      books: [],
      categories: []
    };
  },
  mounted() {
    // console.log(books);
  },
  created() {}
};
</script>
