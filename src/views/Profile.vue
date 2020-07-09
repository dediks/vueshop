<template>
  <div>
    <v-subheader>Your Profile</v-subheader>
    <v-card flat>
      <v-container>
        <v-simple-table>
          <tbody>
            <tr v-for="(value, key) in user.data.customerLogin" :key="key">
              <td v-if="key != 'id'">{{ key }}</td>
              <td v-if="key != 'id'">{{ value }}</td>
            </tr>
          </tbody>
        </v-simple-table>
      </v-container>
    </v-card>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters({
      user: "auth/user"
    })
  },
  created() {
    this.test();
  },
  methods: {
    test() {
      console.log(this.user);
    }
  }
};
</script>