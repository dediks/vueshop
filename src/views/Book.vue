<template>
  <div>
    <v-card v-if="findBookBy.slug">
      <v-img :src="getImage(findBookBy.cover)" class="white--text" height="200px">
        <v-card-title class="fill-height align-end" v-text="findBookBy.title"></v-card-title>
      </v-img>

      <v-card-text>
        <v-simple-table dense>
          <tbody>
            <tr>
              <td>
                <v-icon>mdi-account-tie</v-icon>Author
              </td>
              <td>{{ findBookBy.author}}</td>
            </tr>
            <tr>
              <td>
                <v-icon>mdi-bullhorn</v-icon>Publisher
              </td>
              <td>{{ findBookBy.publisher}}</td>
            </tr>
            <!-- <tr>
                    <td><v-icon>mdi-weight</v-icon> Weight</td>
                    <td>{{ findBookBy.weight }} kg</td>
                </tr>
                <tr>
                    <td><v-icon>mdi-format-list-bulleted</v-icon> Stock</td>
                    <td>{{ findBookBy.stock}} item</td>
                </tr>
                <tr>
                    <td><v-icon>mdi-eye</v-icon>  Views</td>
                    <td>{{ findBookBy.views}}</td>
                </tr>
                <tr>
                    <td><v-icon>mdi-cash</v-icon>  Price</td>
                    <td class="orange--text">Rp {{ findBookBy.price.toLocaleString('id-ID')}}</td>
            </tr>-->
          </tbody>
        </v-simple-table>Description:
        <br />
        {{ findBookBy.description }}
        <!-- <br />Categories:
        <v-chip
          v-for="category in findBookBy.categories"
          :key="category.id"
          :to="'/category/'+category.slug"
          small
        >{{ category.name }}</v-chip>-->
      </v-card-text>
      <v-card-actions>
        <v-btn block color="success" @click="buy" :disabled="findBookBy.stock==0">
          <v-icon>mdi-cart-plus</v-icon>&nbsp;
          BUY
        </v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import gql from "graphql-tag";
import { GET_BOOK_BY_SLUG } from "../constants/graphql";

export default {
  data: () => ({
    findBookBy: {}
  }),
  created() {
    // this.go();
    // console.log(this.$route.params.slug);
  },
  computed: {
    ...mapGetters({
      carts: "cart/carts"
    })
  },
  apollo: {
    // 'tags' data property on vue instance
    findBookBy: {
      query: GET_BOOK_BY_SLUG,
      variables() {
        return {
          identifier: "slug",
          operator: "=",
          value: this.$route.params.slug
        };
      }
    }
  },
  methods: {
    ...mapActions({
      addCart: "cart/add",
      setAlert: "alert/set"
    }),
    buy() {
      this.addCart(this.findBookBy);
      //this.$store.dispatch('cart/add', this.findBookBy)
      this.setAlert({
        status: true,
        color: "success",
        text: "Added to cart"
      });
    },
    go() {
      let { slug } = this.$route.params;
      let url = "/findBookBys/slug/" + slug;
      url = url + "?page=" + this.page;
      url = encodeURI(url);

      // console.log(slug);
      // this.$apollo.queries.findBookBy.variables.value = slug;
    }
  }
};
</script>
