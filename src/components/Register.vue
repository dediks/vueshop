<template>
  <v-card>
    <v-toolbar dark color="primary">
      <v-btn icon dark @click.native="close">
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-toolbar-title>Register!</v-toolbar-title>
    </v-toolbar>
    <v-divider></v-divider>

    <v-container fluid>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-text-field
          v-model="name"
          :rules="nameRules"
          :counter="255"
          label="Name"
          required
          append-icon="mdi-user"
        ></v-text-field>
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
        <v-checkbox
          v-model="checkbox"
          :rules="[v => !!v || 'You must agree to continue!']"
          label="Do You agree with our Privacy Policy?"
          required
        ></v-checkbox>
        <div class="text-xs-center">
          <v-btn color="accent lighten-1" :disabled="!valid" @click="createCustomer">
            Register
            <v-icon right dark>mdi-account-plus</v-icon>
          </v-btn>
          <v-btn @click="clear">
            Reset
            <v-icon right dark>mdi-lock-reset</v-icon>
          </v-btn>
        </div>
      </v-form>
    </v-container>
  </v-card>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import { CUSTOMER_REGISTER, All_CUSTOMERS_QUERY } from "../constants/graphql";
export default {
  name: "register",
  data() {
    return {
      valid: true,
      name: "",
      nameRules: [
        v => !!v || "Name is required",
        v => (v && v.length <= 255) || "Name must be less than 255 characters"
      ],
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
      ],
      checkbox: false
    };
  },
  computed: {
    ...mapGetters({
      user: "auth/user"
    })
  },
  methods: {
    ...mapActions({
      setAlert: "alert/set",
      setAuth: "auth/set"
    }),
    createCustomer() {
      const name = this.name;
      const email = this.email;
      const password = this.password;

      if (this.$refs.form.validate()) {
        this.$apollo
          .mutate({
            mutation: CUSTOMER_REGISTER,
            variables: {
              name: name,
              email: email,
              password: password
            },
            // Optimistic UI
            // Will be treated as a 'fake' result as soon as the request is made
            // so that the UI can react quickly and the user be happy
            optimisticResponse: {
              __typename: "Mutation",
              createCustomer: {
                __typename: "Customer",
                id: null,
                name,
                password,
                email
              }
            }
          })
          .then(data => {
            // Result
            console.log(data);
            this.setAuth(data);
            this.setAlert({
              status: true,
              color: "success",
              text: "Register success"
            });
            this.close();
          })
          .catch(error => {
            // Error
            console.error(error);
            // We restore the initial user input
            // this.newTag = newTag;
          });

        // this.axios
        // .post("/register", formData)
        // .then(response => {
        //   let { data } = response.data;
        //   this.setAuth(data);
        //   this.setAlert({
        //     status: true,
        //     color: "success",
        //     text: "Register success"
        //   });
        //   this.close();
        // })
        // .catch(error => {
        //   let { data } = error.response;
        //   this.setAlert({
        //     status: true,
        //     color: "error",
        //     text: data.message
        //   });
        // });
      }
    },
    close() {
      this.$emit("closed", false);
    },
    clear() {
      this.$refs.form.reset();
    }
  }
};
</script>