<template>
  <v-content>
    <v-container class="fill-height" fluid>
      <v-row align="center" justify="center">
        <v-col cols="12" sm="8" md="4">
          <v-card class="elevation-12">
            <v-toolbar color="primary" dark text>
              <v-toolbar-title>Login form</v-toolbar-title>
              <v-spacer />
            </v-toolbar>
            <v-card-text>
              <v-form>
                <v-text-field
                  v-if="!options.isLoggingIn"
                  v-model="user.name"
                  prepend-icon="mdi-person"
                  label="Name"
                ></v-text-field>
                <v-text-field v-model="user.email" prepend-icon="mdi-at" label="Email" type="email"></v-text-field>
                <v-text-field
                  v-model="user.password"
                  prepend-icon="mdi-textbox-password"
                  label="Password"
                  type="password"
                ></v-text-field>
                <v-text-field
                  v-if="!options.isLoggingIn"
                  v-model="user.confirmPassword"
                  prepend-icon="mdi-textbox-password"
                  label="Confirm password"
                  type="password"
                ></v-text-field>
                <v-checkbox
                  v-if="options.isLoggingIn"
                  v-model="options.shouldStayLoggedIn"
                  label="Stay logged in?"
                  hide-details="hide-details"
                ></v-checkbox>

                <v-btn
                  v-if="options.isLoggingIn"
                  @click.prevent="authenticate"
                  block="block"
                  type="submit"
                  text
                  color="primary"
                >SIGN IN</v-btn>
                <v-btn
                  v-else
                  @click.prevent="register"
                  block="block"
                  type="submit"
                  text
                  color="primary"
                >SIGN UP</v-btn>
              </v-form>
            </v-card-text>
            <v-card-subtitle align="center">
              <div v-if="options.isLoggingIn">
                <span class="mr-2">Don't have an account?</span>
                <v-btn class="mr-2" @click="options.isLoggingIn = false">SIGN UP</v-btn>
              </div>
              <div v-else>
                <span class="mr-2">Already have an account?</span>
                <v-btn class="mr-2" @click="options.isLoggingIn = true">SIGN IN</v-btn>
              </div>
            </v-card-subtitle>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-content>
</template>

<script>
import UserService from "../services/UserService";
import NotifyNotification from "../lib/notify";

export default {
  data() {
    return {
      user: {
        name: "",
        email: "",
        password: ""
      },
      options: {
        isLoggingIn: true,
        shouldStayLoggedIn: true
      }
    };
  },
  methods: {
    register() {
      var data = {
        name: this.user.name,
        email: this.user.email,
        password: this.user.password
      };

      UserService.register(data)
        .then(response => {
          var notifyNotification = new NotifyNotification(
            "Inscription réussie !",
            "",
            "ok"
          );
          notifyNotification.eslint();

          this.authenticate();
        })
        .catch(err => {
          var notifyNotification = new NotifyNotification(
            "Erreur : Impossible d'enregistrer l'utilisateur",
            err,
            "error"
          );
          notifyNotification.eslint();
        });
    },
    authenticate() {
      var data = {
        email: this.user.email,
        password: this.user.password
      };

      UserService.authenticate(data)
        .then(response => {
          var notifyNotification = new NotifyNotification(
            "Connexion réussie",
            "Vous allez être redirigé !",
            "ok"
          );
          notifyNotification.eslint();

          localStorage.setItem("xtoken", response.data.data.token);
          localStorage.setItem("name", response.data.data.user.name);
          localStorage.setItem("email", response.data.data.user.email);

          this.$router.push("/");
        })
        .catch(err => {
          var notifyNotification = new NotifyNotification(
            "Erreur",
            "Identifiants incorrects (" + err + ")",
            "error"
          );
          notifyNotification.eslint();
        });
    }
  }
};
</script>
