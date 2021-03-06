<template>
  <div class="receipt">
    <div class="receipt-header">
      <h3>Bitcoin Withdrawal Receipt</h3>
      <h6>Saved to Transaction History</h6>
    </div>
    <div class="receipt-details">
      <!-- Recipient -->
      <div class="d-flex flex-row justify-content-between align-items-baseline">
        <div class="receipt-label">To</div>
        <div class="receipt-value">{{ transaction.addr }}</div>
      </div>
      <!-- Amount -->
      <div class="d-flex flex-row justify-content-between align-items-baseline">
        <div class="receipt-label">Sending</div>
        <div class="receipt-value">{{ transaction.amt | units }} <units-badge /></div>
      </div>
      <div class="d-flex flex-row justify-content-end"><div class="receipt-value">$13.26</div></div>
      <!-- Add Tx Fee Field -->
      <!-- New Balance -->
      <div class="d-flex flex-row justify-content-between align-items-baseline">
        <div class="receipt-label">New BTC Balance</div>
        <div class="receipt-value">{{ remainingBalance | units }} <units-badge /></div>
      </div>
      <div class="d-flex flex-row justify-content-end">
        <div class="receipt-value">${{ ((parseInt(remainingBalance) / 100000000) * rate).toFixed(3) }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BitcoinReceipt',
  data() {
    return {
      rate: '',
      units: '',
      remainingBalance: '',
    };
  },
  async created() {
    this.units = this.$store.state.settings.units;
    const baseUrl = this.$store.state.settings.baseUrl;

    // Get BTC Balance
    try {
      const { balance } = (await this.$http.get(`${baseUrl}:3002/v1/pages/lnd`)).data;
      this.remainingBalance = balance.wallet.totalBalance - parseInt(this.transaction.amt);
    } catch (err) {
      this.$notify({ group: 'alerts', type: 'error', title: 'Error', text: 'Unable to calculate balance', position: 'top center' });
    }

    // Get BTC price
    try {
      const { USD } = (await this.$http.get('https://min-api.cryptocompare.com/data/price?fsym=BTC&tsyms=USD')).data;
      this.rate = USD;
    } catch (err) {
      this.$notify({ group: 'alerts', type: 'error', title: 'Error', text: 'Error getting conversion rate.', position: 'top center' });
    }
  },
  computed: {
    transaction: {
      get() {
        return this.$store.state.newTransaction;
      },
    },
  },
};
</script>

<style lang="scss" scoped>
.receipt-label {
  opacity: 0.4;
  font-size: 13px;
  font-weight: 900;
  line-height: 1.3;
  letter-spacing: 1px;
  color: #333333;
  text-transform: uppercase;
}

.receipt-value {
  font-size: 15px;
  font-weight: bold;
  text-align: right;
  color: #333333;
  word-break: break-word;
}

.receipt-header {
  position: relative;
  max-width: 42em;
  margin: 0 auto;
  padding: 1em;
  color: #333333;
  border-bottom: 3px solid #f5f5f5;

  h3 {
    font-size: 24px;
    font-weight: 900;
    color: #333333;
  }

  h6 {
    opacity: 0.5;
    font-size: 18px;
    font-weight: 500;
    color: #333333;
  }
}

.receipt-details {
  position: relative;
  max-width: 42em;
  margin-right: auto;
  margin-left: auto;
  padding: 1em;
  color: #333333;
}

.receipt-details .justify-content-between {
  margin: 1.25rem 0 0 0;
  align-items: baseline !important;
}

.date-container {
  border-radius: 10px;
  background-color: #ffffff;
  border: 1px solid #d7d8d9;
}

.date-badge span {
  font-size: 10px;
  font-weight: bold;
  text-transform: uppercase;
}

.date-badge {
  max-width: 45px;
  max-height: 45px;
  padding-left: 0;
  padding-right: 0;
  border-radius: 9.5px;
  border: none;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
}

.date-badge .col {
  padding-left: 0;
  padding-right: 0;
}

.date-badge .month {
  font-size: 10px;
  font-weight: 900;
  text-align: center;
  color: #8d8e8e;
  margin-top: 3px;
}

.date-badge .day {
  font-size: 16px;
  font-weight: 500;
  text-align: center;
  color: #333333;
  margin-top: -2px;
}

.date-time {
  font-size: 15px;
  text-align: center;
  color: #333333;
  border-left: 1px solid #d7d8d9;
  padding-left: 10px;
}
</style>
