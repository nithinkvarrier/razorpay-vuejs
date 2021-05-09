<template>
  <div class="container">
    <button @click="pay">Pay</button>
  </div>
</template>

<script>
export default {
  methods: {
    async pay() {
      // Customer details and amount
      const data = {
        amount: 1000,
        name: '',
        email: '',
        phone: '',
      };

      this.axios
        .post('our Firebase function root url + /razorpay/createPayment', data)
        .then((res) => {
          console.log(res);
          let order = res.data;
          var options = {
            key: 'Add your razorpay key here',
            amount: order.amount_due,
            currency: order.currency,
            description: 'Payment description',
            order_id: order.id,
            prefill: {
              name: data.name,
              email: data.email,
              contact: data.phone,
            },
            theme: {
              color: '#000000', // Set your website theme color
            },
            handler: (response) => {
              this.verifySignature(response);
            },
          };

          var rzp = new Razorpay(options);
          rzp.open();
        })
        .catch((err) => {
          console.error(err);
        });
    },
    async verifySignature(response) {
      this.axios
        .post(
          'our Firebase function root url + /razorpay/verifyPayment',
          response
        )
        .then((response) => {
          console.log(response);
          alert('payment received');
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
button {
  background-color: #000;
  color: #fff;
  padding: 10px 15px;
  border: none;
  width: 100px;
  font-size: 18px;
  text-transform: uppercase;
}
button:hover {
  cursor: pointer;
}
</style>
