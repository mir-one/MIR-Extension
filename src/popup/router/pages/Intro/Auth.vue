<template>
  <div class="settings">
    <h4>Almost Done</h4>
    <p class="help-text">Just enter your MIR.Cube Node password and we'll take care of the rest.</p>
    <section>
      <b-form @submit="onSubmit">
        <b-form-group class="password-label" label="MIR.Cube Node Password" label-for="MIR.Cube Node Password">
          <b-form-input id="password" type="password" v-model="password" required></b-form-input>
        </b-form-group>
        <div class="buttons">
          <b-button class="btn mir-button" type="submit" :disabled="pending === true">
            <span v-if="!pending">Sign In</span> <span v-if="pending" class="spinner-border spinner-border-sm" role="status"></span> <span v-if="pending">Connecting</span>
          </b-button>
        </div>
      </b-form>
    </section>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: `Auth`,
  data() {
    return {
      password: '',
      pending: false,
    };
  },
  methods: {
    onSubmit: async function(evt) {
      evt.preventDefault();
      try {
        this.pending = true;
        await this.$store.dispatch('login', { password: this.password });
        this.$router.push('/lightning');
      } catch (err) {
        this.$notify({ group: 'alerts', type: 'error', title: 'Authentication Failed', text: `Please verify your password and try again.`, position: 'top center' });
      } finally {
        this.pending = false;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.settings {
  margin: 0 1.5rem;

  h4 {
    font-size: 24px;
    font-weight: bold;
    line-height: 1.5em;
    color: #fff;
  }

  .help-text {
    margin: 1.5em 0;
    line-height: 1.5em;
    color: #a29bbc;
  }

  .password-label {
    font-weight: bold;
    font-size: 18px;
  }

  .popup-main {
    height: 100% !important;
  }

  .help-link {
    color: #0056ff;
    text-decoration: none;
  }

  .b-form-group input {
    height: 55px;
    color: #fff;
  }

  .buttons {
    position: absolute;
    bottom: 2em;
    left: 0;
    right: 0;
    padding: 0 2em 0 1.8em;
  }

  .mir-button {
    border: none;
    width: 100%;
    padding: 1rem;
    font-weight: bold;
    background-image: linear-gradient(to right, #0056ff, #0056ff);
  }

  #password {
    font-weight: bold;
    font-size: 18px;
  }
}
</style>
