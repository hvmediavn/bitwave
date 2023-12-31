<template>
  <v-container
    grid-list-sm
  >
    <v-layout
      row
      justify-space-around
    >
      <v-flex xs12 md8 lg6 xl4>
        <v-card class="my-3">
          <v-card-title>
            <h3>
              <v-icon>warning</v-icon>
              Report Content
            </h3>
          </v-card-title>
          <hr>
          <v-card-text>
            <v-form ref="report">
              <v-layout column>
                <v-flex>
                  <v-text-field
                    v-model="name"
                    label="legal name"
                    name="name"
                    color="primary"
                    required
                    :rules="[ rules.required ]"
                    :loading="submitting"
                    :disabled="submitting || submitted"
                    validate-on-blur
                    outline
                  ></v-text-field>
                  <v-text-field
                    v-model="email"
                    label="email"
                    name="_replyto"
                    type="email"
                    color="primary"
                    required
                    :rules="[ rules.required, rules.email ]"
                    :loading="submitting"
                    :disabled="submitting || submitted"
                    validate-on-blur
                    outline
                  ></v-text-field>
                  <v-textarea
                    v-model="report"
                    name="report"
                    outline
                    label="message"
                    color="primary"
                    required
                    :rules="[ rules.required ]"
                    :loading="submitting"
                    :disabled="submitting || submitted"
                    counter
                  ></v-textarea>
                </v-flex>
                <v-btn
                  color="red"
                  :loading="submitting"
                  :disabled="submitting || submitted"
                  @click="createReport"
                >{{ submitted ? 'success' : 'submit' }}</v-btn>
                <v-flex>
                  <v-alert
                    v-show="submitted"
                    :color="success ? 'success' : 'error'"
                    class="mt-4"
                  >{{ success ? `Report sent. Ref: ${reportId.id}` : 'Error Occured' }}</v-alert>
                </v-flex>
              </v-layout>
            </v-form>
          </v-card-text>
        </v-card>

      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
  import { db } from '@/plugins/firebase.js'

  export default {
    name: 'report',

    data() {
      return {
        name: '',
        email: '',
        report: '',

        reportId: '',

        valid: true,
        submitting: false,
        submitted: false,
        success: true,

        rules: {
          required: value => !!value || 'Required.',
          email: value => {
            const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
            return pattern.test(value) || 'Invalid e-mail.'
          },
        },
      }
    },

    methods: {
      async createReport () {
        this.valid = this.$refs.report.validate();

        if (!this.valid) return;

        this.submitting = true;
        this.success = true;

        let ipinfo = {
          ip: '',
          hostname: '',
          city: '',
          region: '',
          country: '',
          loc: '',
          postal: '',
          phone: '',
          org: '',
        };

        try {
          const res = await this.$axios.get(`https://ipinfo.io/json?token=${process.env.IPINFO_TOKEN}`);
          ipinfo = res.data;
        } catch (e) {
          console.log(e);
        }

        try {
          const reportRef = db.collection('reports');
          this.reportId = await reportRef.add({
            name: this.name,
            email: this.email,
            report: this.report,
            resolved: false,
            date:  new Date( Date.now() ),
            ipinfo,
          });
        } catch (e) {
          console.log(e);
          this.success = false;
        }

        this.submitting = false;
        this.submitted = true;
      },
    },

    computed: {},
  }
</script>
