<template>
  <v-container ma-0 pa-0 grid-list-xl justify-center id="site-tester-tool">
    <v-layout
      row
      align-center
      justify-center
      id="places-search"
      class="step step-1"
      v-show="step1"
    >
      <v-flex>
        <v-card light flat>
          <v-card-text class="text-xs-center">
            <v-text-field
              class="headline"
              box
              full-width
              hide-details
              clearable
              label="Practice Name - Start typing your practice name, then select your
              practice from the dropdown"
              ref="autocomplete"
              placeholder="Enter your practice name..."
              onfocus="value = ''"
              :value="
                practiceInfo.streetAddress.length == 0
                  ? ''
                  : practiceInfo.placeName +
                    ', ' +
                    practiceInfo.streetAddress +
                    ', ' +
                    practiceInfo.city +
                    ', ' +
                    practiceInfo.state +
                    ' ' +
                    practiceInfo.zipcode
              "
            ></v-text-field>

            <v-btn
              large
              block
              dark
              color="#aaa"
              id="next-step"
              class="headline"
              :disabled="practiceInfo.streetAddress.length == 0"
              @click="(step2 = true), (step1 = false)"
            >
              Next
              <v-icon right dark>arrow_right_alt</v-icon>
            </v-btn>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
    <v-layout
      row
      wrap
      align-center
      justify-center
      class="step step-2"
      v-show="step2"
    >
      <v-flex xs12>
        <v-card light flat>
          <v-card-text>
            <v-layout row wrap align-center justify-center>
              <v-flex xs12>
                <v-card light flat>
                  <v-text-field
                    id="street_address"
                    class="headline"
                    box
                    full-width
                    hide-details
                    name="street_address"
                    label="Street Address"
                    :value="practiceInfo.streetAddress"
                  ></v-text-field>
                </v-card>
              </v-flex>
              <v-flex xs12 sm4>
                <v-card light flat>
                  <v-text-field
                    xs4
                    id="city"
                    class="headline"
                    box
                    full-width
                    hide-details
                    name="city"
                    label="City"
                    :value="practiceInfo.city"
                  ></v-text-field>
                </v-card>
              </v-flex>
              <v-flex xs12 sm4>
                <v-card light flat>
                  <v-text-field
                    xs4
                    id="state"
                    class="headline"
                    box
                    full-width
                    hide-details
                    name="state"
                    label="State"
                    :value="practiceInfo.state"
                  ></v-text-field>
                </v-card>
              </v-flex>
              <v-flex xs12 sm4>
                <v-card light flat>
                  <v-text-field
                    xs4
                    id="zip"
                    class="headline"
                    box
                    full-width
                    hide-details
                    name="zip"
                    label="Zip"
                    :value="practiceInfo.zipcode"
                  ></v-text-field>
                </v-card>
              </v-flex>
              <v-flex xs12>
                <v-card light flat>
                  <v-text-field
                    id="website"
                    class="headline"
                    box
                    full-width
                    hide-details
                    name="website"
                    label="Website"
                    :value="practiceInfo.website"
                  ></v-text-field>
                </v-card>
              </v-flex>
              <v-flex xs12>
                <v-btn
                  large
                  block
                  dark
                  color="#aaa"
                  id="diagnose"
                  class="headline"
                  @click="(step3 = true), (step2 = false)"
                >
                  Next
                  <v-icon right dark>arrow_right_alt</v-icon>
                </v-btn>
              </v-flex>
            </v-layout>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>

    <v-layout
      row
      wrap
      align-center
      justify-center
      class="step step-3"
      v-show="step3"
    >
      <v-flex xs12>
        <v-card light flat>
          <v-card-text>
            <v-form v-model="klaviyo.valid">
              <v-layout row wrap align-center justify-center>
                <v-flex xs12 md4>
                  <v-card light flat>
                    <v-text-field
                      v-model="klaviyo.firstName"
                      :rules="klaviyo.nameRules"
                      :counter="10"
                      label="First Name"
                      required
                    ></v-text-field>
                  </v-card>
                </v-flex>
                <v-flex xs12 md4>
                  <v-card light flat>
                    <v-text-field
                      v-model="klaviyo.lastName"
                      :rules="klaviyo.nameRules"
                      :counter="10"
                      label="Last Name"
                      required
                    ></v-text-field>
                  </v-card>
                </v-flex>
                <v-flex xs12 md4>
                  <v-card light flat>
                    <v-text-field
                      v-model="klaviyo.email"
                      :rules="klaviyo.emailRules"
                      label="E-mail"
                      required
                    ></v-text-field>
                  </v-card>
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
                    Diagnose
                    <v-icon right dark>center_focus_strong</v-icon>
                  </v-btn>
                </v-flex>
              </v-layout>
            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>

    <v-layout row align-center justify-center>
      <v-progress-circular
        v-if="loading"
        :size="120"
        :width="11"
        color="rgb(229, 12, 136)"
        indeterminate
        >Diagnosing...</v-progress-circular
      >
    </v-layout>
    <v-layout row wrap align-start justify-center id="response">
      <v-flex
        xs12
        md4
        v-for="(category, prop, index) in lighthouseResponse.data
          .lighthouseResult.categories"
        :key="category.id"
        :id="category.id"
        class="category"
      >
        <v-card light flat>
          <v-card-text>
            <h3
              class="display-1 font-weight-black"
              style="margin-bottom: 1.5rem;"
            >
              {{ category.title }}
              <v-progress-circular
                v-if="category.score < 0.5"
                :size="66"
                :width="7"
                :value="category.score * 100"
                color="red"
                class="score"
                >{{ category.score * 100 }}</v-progress-circular
              >
              <v-progress-circular
                v-else
                :size="66"
                :width="7"
                :value="category.score * 100"
                :color="category.score < 0.8 ? 'orange' : 'green'"
                class="score"
                >{{ category.score * 100 }}</v-progress-circular
              >
            </h3>
            <v-btn
              large
              block
              dark
              color="#aaa"
              id="diagnose"
              class="toggle-audits headline"
              @click="isActive[index] = !isActive[index]"
            >
              <span v-if="!isActive[index]">
                Show Details
                <v-icon right dark>visibility</v-icon>
              </span>
              <span v-else>
                Hide Details
                <v-icon right dark>visibility_off</v-icon>
              </span>
            </v-btn>
            <v-layout
              row
              wrap
              align-start
              justify-center
              v-show="isActive[index]"
              :class="category.id + '-audits'"
            >
              <v-flex
                xs12
                v-for="auditRef in category.auditRefs"
                :key="auditRef.id"
                v-if="auditRef.weight > 0"
              >
                <v-card large block light flat color="#f2f2f2">
                  <v-card-text>
                    <v-layout row wrap align-start justify-center>
                      <v-flex xs12>
                        <h4 class="headline">
                          {{
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].title
                          }}
                        </h4>
                      </v-flex>
                      <v-flex xs6 subheading>
                        Result:
                        <span
                          :class="[
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].score > 0.79
                              ? 'green--text'
                              : '',
                            0.49 <
                              lighthouseResponse.data.lighthouseResult.audits[
                                auditRef.id
                              ].score &&
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].score < 0.8
                              ? 'orange--text'
                              : '',
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].score < 0.5
                              ? 'red--text'
                              : ''
                          ]"
                        >
                          {{
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].displayValue
                          }}
                        </span>
                      </v-flex>
                      <v-flex xs6 subheading>
                        Score:
                        <span
                          :class="[
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].score > 0.79
                              ? 'green--text'
                              : '',
                            0.49 <
                              lighthouseResponse.data.lighthouseResult.audits[
                                auditRef.id
                              ].score &&
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].score < 0.8
                              ? 'orange--text'
                              : '',
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].score < 0.5
                              ? 'red--text'
                              : ''
                          ]"
                        >
                          {{
                            lighthouseResponse.data.lighthouseResult.audits[
                              auditRef.id
                            ].score * 100
                          }}
                        </span>
                      </v-flex>
                    </v-layout>
                  </v-card-text>
                </v-card>
              </v-flex>
            </v-layout>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      apiKey: "AIzaSyCuVoOTyWCAmLDA_UoyX98BW-qLQzx2OZs",
      practiceInfo: {
        placeName: "",
        streetAddress: "",
        city: "",
        state: "",
        zipcode: "",
        website: ""
      },
      step1: true,
      step2: false,
      step3: false,
      klaviyo: {
        apiKey: "pk_f93763b456125ce1e2b2c1e8cc2e757937",
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
      klaviyoResponse: {
        id: "",
        email: ""
      },
      loading: false,
      loadingProgress: 0,
      isActive: {
        0: false,
        1: false,
        2: false,
        3: false
      },
      lighthouseResponse: {
        data: {
          lighthouseResult: {
            audits: {},
            categories: {}
          }
        }
      }
    };
  },
  mounted() {
    this.autocomplete = new google.maps.places.Autocomplete(
      this.$refs.autocomplete.$refs.input,
      {
        types: ["establishment"],
        componentRestrictions: { country: "us" },
        fields: [
          "name",
          "address_components",
          "geometry",
          "website",
          "place_id"
        ]
      }
    );
    this.autocomplete.addListener("place_changed", () => {
      let place = this.autocomplete.getPlace();
      let addressComponents = place.address_components;
      let place_name = place.name;
      let street = "",
        number = "",
        locality = "",
        administrative = "",
        postal_code = "";
      let website = place.website;

      for (var i = 0, i_len = addressComponents.length; i < i_len; i++) {
        var addressType = addressComponents[i].types[0];

        switch (addressType) {
          case "street_number":
            number = addressComponents[i]["long_name"];
            break;

          case "route":
            street = addressComponents[i]["long_name"];
            break;

          case "locality":
            locality = addressComponents[i]["long_name"];
            break;

          case "administrative_area_level_1":
            administrative = addressComponents[i]["long_name"];
            break;

          case "postal_code":
            postal_code = addressComponents[i]["long_name"];
            break;
        }
      }

      this.$set(this.practiceInfo, "placeName", place_name);
      this.$set(this.practiceInfo, "streetAddress", number + " " + street);
      this.$set(this.practiceInfo, "city", locality);
      this.$set(this.practiceInfo, "state", administrative);
      this.$set(this.practiceInfo, "zipcode", postal_code);
      this.$set(this.practiceInfo, "website", website);
    });
  },
  methods: {
    diagnose: function() {
      let lighthouseApi =
        "https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=" +
        this.practiceInfo.website +
        "&category=best-practices&category=performance&category=seo&strategy=mobile&key=" +
        this.apiKey;
      /*let lighthouseApi = "http://localhost:3000/pageSpeed";*/
      this.loading = true;
      axios
        .get(lighthouseApi)
        .then(response => {
          this.loading = false;
          this.lighthouseResponse = response;
        })
        .catch(error => {
          this.loading = false;
          console.log(error);
        });
    },
    klaviyoSubscribe() {
      axios({
        method: "post",
        url: "https://a.klaviyo.com/api/v2/list/MTENqF/subscribe",
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json"
        },
        data: {
          api_key: this.klaviyo.apiKey,
          profiles: [
            {
              email: this.klaviyo.email,
              first_name: this.klaviyo.firstName,
              last_name: this.klaviyo.lastName
            }
          ]
        }
      })
        .then(response => {
          (this.klaviyoResponse = response), console.log(this.klaviyoResponse);
        })
        .catch(e => {
          console.error(e);
          console.log(this.klaviyoResponse);
        });
    }
  }
};
</script>

<style scoped></style>
