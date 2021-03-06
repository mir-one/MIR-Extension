<template>
  <div>
    <b-navbar>
      <img id="back-button" src="~assets/images/back.svg" class="d-inline-block align-top" alt="back" @click.prevent="$router.back()" />
      <h3 class="page-header">Review Lightning Payment</h3>
      <img id="forward-button" src="~assets/images/back.svg" />
    </b-navbar>
    <main class="popup-main">
      <section class="data-group">
        <p><span class="payment-badge">You're Paying</span></p>
        <p class="pay-to">{{ this.destination | truncate }}...</p>
        <p>{{ this.description }}</p>
        <hr />
        <p class="pay-amount">{{ this.amount | units }} <units-badge /></p>
        <p class="pay-fiat">{{ fiatValue }}</p>
        <a class="btn mir-button btn-block" @click="confirmPayment" name="button" :disabled="pending === true">
          <span v-if="!pending">Confirm Send</span> <span v-if="pending" class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
          <span v-if="pending">Sending</span>
        </a>
      </section>
      <section class="wallet-options">
        <p class="payment-info">{{ (this.chBalance - this.amount) | units }} <units-badge /></p>
        <p class="payment-details">New Lightning Balance</p>
      </section>
    </main>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: `LightningReview`,
  data() {
    return {
      units: '',
      expiry: '',
      amount: '',
      fiatValue: '',
      destination: '',
      description: '',
      chBalance: '',
      pending: false,
    };
  },
  async created() {
    this.units = this.$store.state.settings.units;
    const baseUrl = this.$store.state.settings.baseUrl;
    try {
      const { balance } = (await this.$http.get(`${baseUrl}:3002/v1/pages/lnd`)).data;
      this.chBalance = balance.channel.balance;
    } catch (err) {
      this.$notify({ group: 'alerts', type: 'error', title: 'Error', text: `${err.response.data}`, position: 'top center' });
    }

    const invoice = this.$store.state.invoice;
    try {
      const paymentInfo = (await this.$http.get(`${baseUrl}:3002/v1/lnd/lightning/invoice?paymentRequest=${invoice}`)).data;
      this.expiry = paymentInfo.expiry;
      this.amount = paymentInfo.numSatoshis;
      this.destination = paymentInfo.destination;
      this.description = paymentInfo.description;
    } catch (err) {
      this.$notify({ group: 'alerts', type: 'error', title: 'Error', text: `${err.response.data}`, position: 'top center' });
    }

    try {
      const { USD } = (await this.$http.get('https://min-api.cryptocompare.com/data/price?fsym=BTC&tsyms=USD')).data;
      this.fiatValue = '$' + ((this.amount / 100000000) * USD).toFixed(2);
    } catch (err) {
      this.$notify({ group: 'alerts', type: 'error', title: 'Error', text: `Error getting conversion rate`, position: 'top center' });
    }
  },
  methods: {
    async confirmPayment() {
      const baseUrl = this.$store.state.settings.baseUrl;
      this.pending = true;
      try {
        await this.$http.post(`${baseUrl}:3002/v1/lnd/lightning/payInvoice`, { paymentRequest: this.$store.state.invoice });
        this.$router.push({ path: '/lightning/pay/success' });
      } catch (err) {
        this.$notify({ group: 'alerts', type: 'error', title: 'Error', text: `${err.response.data}`, position: 'top center' });
      } finally {
        this.pending = false;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.navbar {
  background-color: #333333;
}

.popup-main {
  color: #fff !important;
  background-color: #333333;
  padding-top: 0;
  padding-bottom: 0;
}

.page-header {
  font-size: 20px;
  font-weight: bold;
  color: #fff;
  margin-top: 0.75rem;
}

.data-group {
  border: none;
  margin-top: 2rem;
  min-height: 350px;

  textarea {
    background-color: rgba(255, 255, 255, 0.03);
  }

  hr {
    border: 1px solid #fff;
    opacity: 0.1;
  }

  p {
    text-align: center;

    .payment-badge {
      border-radius: 15px;
      background-color: #333333;
      margin: 0 auto;
      padding: 7px 10px;
      text-transform: uppercase;
      letter-spacing: 1.25px;
      font-size: 15px;
      font-weight: bold;
    }
  }

  .pay-to {
    font-size: 22px;
    font-weight: bold;
  }

  .pay-amount {
    letter-spacing: -0.9px;
    font-size: 36px;
  }

  .pay-fiat {
    letter-spacing: -0.5px;
    font-size: 20px;
    margin-top: -10px;
    margin-bottom: 10px;
  }
}

.wallet-options {
  background-color: #333333;
  padding: 3rem 1.5rem 3rem;
  p {
    text-align: center;
  }
}

.wallet-options > a.ext-link > img {
  max-height: 11px !important;
  margin-left: 10px !important;
  margin-top: -2px !important;
}

.ext-link {
  padding-top: 0.33em;
  padding-bottom: 0.33em;
  margin-top: 1rem;
  border-radius: 24px;
  color: #ffffff;
  background-color: rgba(255, 255, 255, 0.1);
  display: block;
  margin: 1rem auto;
}

.payment-info {
  font-size: 24px;
  margin-bottom: 0.8rem;

  span {
    font-size: 12px;
    font-weight: bold;
  }
}

.payment-details {
  font-size: 13px;
  font-weight: 900;
  line-height: 1.3;
  letter-spacing: 1px;
  text-align: center;
  color: #0056ff;
}

.mir-button {
  padding: 1rem;
  margin-top: 1rem;
  margin-bottom: 0.25rem;
  display: inline-block;
  font-size: 16px;
  font-weight: bold;
  border-radius: 4px;
  background-image: linear-gradient(to right, #0056ff, #0056ff);
  -webkit-appearance: none;
  text-decoration: none !important;
  border: none;
}

#back-button {
  cursor: pointer;
}

#forward-button {
  visibility: hidden;
}
</style>
