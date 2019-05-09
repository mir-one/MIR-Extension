<template>
  <div class="settings">
    <h4>Update Your Settings</h4>
    <p class="help-text">
      You'll need to locate your MIR.Cube Node's local IP address to finish setup.
      <a class="help-link" href="https://mir.one/node-help/" target="_blank" rel="noopener">See Our Guide</a>
    </p>
    <form @submit="onSubmit">
      <b-form-group class="base-url" label="MIR.Cube Node IP Address"> <b-form-input v-model="baseUrl" :class="{ error: error }"></b-form-input> </b-form-group>

      <b-form-group class="base-url" label="Preferred BTC Units"> <b-form-select v-model="selectedUnit" :options="units" class="btc-units mb-3"></b-form-select> </b-form-group>
      <div class="buttons">
        <button class="btn mir-button" type="submit" name="button" :disabled="pending === true">
          <span v-if="!pending">Update</span> <span v-if="pending" class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
          <span v-if="pending">Updating</span>
        </button>
      </div>
    </form>
    <p v-if="error" class="report-error">Connection unsuccessful. Please try again or See Our Guide</p>
  </div>
</template>

<script>
import axios from 'axios';
const ipRegex = /^((http:\/\/|https:\/\/)?([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$/;
const domainRegex = /^((http:\/\/|https:\/\/)?([a-zA-Z]|[a-zA-Z][a-zA-Z0-9\-]*[a-zA-Z0-9])\.)*([A-Za-z]|[A-Za-z][A-Za-z0-9\-]*[A-Za-z0-9])$/;

export default {
  name: `Settings`,
  data() {
    return {
      baseUrl: '',
      error: false,
      pending: false,
      units: [{ value: 'sats', text: 'Satoshis (sats)' }, { value: 'btc', text: 'Bitcoin (BTC)' }],
      selectedUnit: 'sats',
    };
  },
  async created() {
    this.baseUrl = this.$store.state.settings.baseUrl;
    this.selectedUnit = this.$store.state.settings.units;
  },
  computed: {
    validIP() {
      return ipRegex.test(this.baseUrl) ? true : false;
    },
    validHost() {
      return domainRegex.test(this.baseUrl) ? true : false;
    },
  },
  methods: {
    onSubmit: async function(evt) {
      evt.preventDefault();
      if (this.validIP || this.validHost) {
        this.setProtocol();
        // test url before updating vuex
        try {
          this.pending = true;
          await axios({ timeout: 10000, url: `${this.baseUrl}:3000/ping` });
          this.$store.dispatch('setBaseUrl', this.baseUrl);
          this.$store.dispatch('setUnits', this.selectedUnit);
          this.$router.push('/lightning');
        } catch (err) {
          this.error = true;
        } finally {
          this.pending = false;
        }
      } else {
        this.$notify({
          group: 'alerts',
          type: 'error',
          title: 'Invalid IP Address',
          text: 'The value you entered is not a valid IP Address. Please try again.',
        });
      }
    },
    setProtocol() {
      // ensure correct protocol
      if (this.baseUrl.includes('https://')) {
        this.baseUrl = this.baseUrl.replace('https://', 'http://');
      } else if (!this.baseUrl.includes('http://')) {
        this.baseUrl = `http://${this.baseUrl}`;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
p {
  color: #fff;
}

* {
  color: #fff;
}

.settings h4 {
  font-weight: bold;
  line-height: 1.5em;
}

.settings .help-text {
  margin: 1.5em 0;
  line-height: 1.5em;
  color: #a29bbc;
}

.settings {
  margin: 0 1.5rem;
}

.popup-main {
  height: 100% !important;
}

.help-link {
  color: #3bccfc !important;
  text-decoration: none;
}

.report-error {
  color: #f0649e;
  font-size: 16px;
  font-weight: 500;
  display: inherit;
  float: right;
}

.report-error img {
  display: inline-block;
  vertical-align: top;
  margin-right: 0.25em;
  height: 25px;
}

.report-error a {
  color: #f0649e;
  padding-bottom: 3px;
  border-bottom: 1px solid #f0649e;
}

.report-success {
  color: #2dcccd;
  float: right;
}

.report-error.level:not(:last-child) {
  margin-bottom: inherit;
}

.b-form-group input {
  height: 55px;
  border-color: #393163;
  color: #fff;
}

.base-url {
  font-weight: bold;
  font-size: 18px;
}

.btc-units {
  height: 55px;
}

.buttons {
  position: fixed;
  bottom: 2em;
  left: 0;
  right: 0;
  padding: 0 2em 0 1.8em;
}

.mir-button {
  padding: 1rem;
  text-decoration: none !important;
  -webkit-appearance: none;
  display: block;
  font-size: 16px;
  font-weight: bold;
  border-radius: 4px;
  background-image: linear-gradient(to right, #5839f5, #9469fe);
  border: none;
  width: 100%;
}
</style>
