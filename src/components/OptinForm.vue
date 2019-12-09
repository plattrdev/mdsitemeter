<template>
  <div>
    <v-form v-model="valid">
      <v-container>
        <v-layout>
          <v-flex xs12 md4>
            <v-text-field
              v-model="klaviyo.firstName"
              :rules="klaviyo.nameRules"
              :counter="10"
              label="First Name"
              required
            ></v-text-field>
          </v-flex>

          <v-flex xs12 md4>
            <v-text-field
              v-model="klaviyo.lastName"
              :rules="klaviyo.nameRules"
              :counter="10"
              label="Last Name"
              required
            ></v-text-field>
          </v-flex>

          <v-flex xs12 md4>
            <v-text-field
              v-model="klaviyo.email"
              :rules="klaviyo.emailRules"
              label="E-mail"
              required
            ></v-text-field>
          </v-flex>

          <v-flex xs12>
            <v-btn
              large
              block
              dark
              color="#aaa"
              id="optin"
              class="headline"
              @click="klaviyoSubscribe()"
            >
              Submit
              <v-icon right dark>arrow_right_alt</v-icon>
            </v-btn>
          </v-flex>
        </v-layout>
      </v-container>
    </v-form>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      klaviyo: {
        valid: false,
        firstName: "",
        lastName: "",
        nameRules: [
          v => !!v || "Name is required",
          v => v.length <= 10 || "Name must be less than 10 characters"
        ],
        email: "",
        emailRules: [
          v => !!v || "E-mail is required",
          v => /.+@.+/.test(v) || "E-mail must be valid"
        ]
      },
      klaviyoResponse: {}
    };
  },
  methods: {
    klaviyoSubscribe() {
      axios
        .post("https://a.klaviyo.com/api/v2/list/MTENqF/subscribe", {
          api_key: "pk_00a728aedd19d9340a8eda5d3bd8467016",
          profiles: [
            {
              email: this.klaviyo.email,
              first_name: this.klaviyo.firstName,
              last_name: this.klaviyo.lastName
            }
          ]
        })
        .then(response => {
          (this.klaviyoResponse = response), console.log(this.klaviyoResponse);
        })
        .catch(e => {
          console.error(e);
        });
    }
  }
};
</script>

<style scoped></style>
