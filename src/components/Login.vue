<template>
  <v-card>
    <v-toolbar dark color="primary">
      <v-btn icon dark @click.native="close">
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-toolbar-title>Login and Start Shopping!</v-toolbar-title>
    </v-toolbar>
    <v-divider></v-divider>
    <v-container fluid>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-text-field
          v-model="email"
          :rules="emailRules"
          label="E-mail"
          required
          append-icon="mdi-email"
        ></v-text-field>
        <v-text-field
          v-model="password"
          :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
          :rules="passwordRules"
          :type="showPassword ? 'text' : 'password'"
          label="Password"
          hint="At least 6 characters"
          counter
          @click:append="showPassword = !showPassword"
        ></v-text-field>

        <div class="text-xs-center">
          <v-btn color="accent lighten-1" :disabled="!valid" @click="login">
            Login
            <v-icon right dark>mdi-lock-open</v-icon>
          </v-btn>
        </div>
      </v-form>
    </v-container>
  </v-card>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import { CUSTOMER_LOGIN_QUERY } from "../constants/graphql";
export default {
  name: "login",
  data() {
    return {
      customerLogin: null,
      skipQuery: true,
      valid: true,
      email: "",
      emailRules: [
        v => !!v || "E-mail is required",
        v =>
          /([a-zA-Z0-9_]{1,})(@)([a-zA-Z0-9_]{2,}).([a-zA-Z0-9_]{2,})+/.test(
            v
          ) || "E-mail must be valid"
      ],
      showPassword: false,
      password: "",
      passwordRules: [
        v => !!v || "Password required.",
        v => (v && v.length >= 6) || "Min 6 characters"
      ]
    };
  },
  computed: {
    ...mapGetters({
      user: "auth/user",
      prevUrl: "prevUrl" // <= ini
    })
  },
  apollo: {
    customerLogin: {
      query: CUSTOMER_LOGIN_QUERY,
      variables() {
        return {
          email: this.email,
          password: this.password
        };
      },
      skip() {
        return this.skipQuery;
      }
    }
  },
  methods: {
    ...mapActions({
      setAlert: "alert/set",
      setAuth: "auth/set"
    }),
    login() {
      if (this.$refs.form.validate()) {
        // let formData = {
        //   email: this.email,
        //   password: this.password
        // };

        this.$apollo.queries.customerLogin.skip = false;
        let test = this.$apollo.queries.customerLogin.refetch();

        test
          .then(data => {
            console.log(data.data);
            if (data.data.customerLogin.id > 0) {
              this.setAuth(data);
              this.setAlert({
                status: true,
                color: "success",
                text: "Login success"
              });
              if (this.prevUrl.length > 0) this.$router.push(this.prevUrl); // tambahkan ini
              this.close();
            } else {
              this.setAlert({
                status: true,
                color: "error",
                text: "Login failed"
              });
            }
          })
          .catch(error => {
            console.log("error" + error);
            this.setAlert({
              status: true,
              color: "error",
              text: "error"
            });
          });
      }
    },
    close() {
      this.$emit("closed", false);
    }
  }
};
</script>