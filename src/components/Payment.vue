<template>
  <div class="flex flex-col items-center justify-center mt-10">
    <div v-if="!paidFor">
      <h1 class="text-bold text-xl text-center pb-20">
        Buy this {{ productChoice.title + " for " + productChoice.price }} €
      </h1>
      <div
        class="h-[400px] rounded-t-xl"
        :style="`background-image: url(${productChoice.image}); background-position: center;background-size: contain; background-repeat: no-repeat;`"
        :title="productChoice.title"
      ></div>
      <p class="px-[200px] pt-20">{{ productChoice.description }}</p>
    </div>

    <div v-if="paidFor">
      <h1>{{ productChoice.title }}</h1>
    </div>

    <div class="p-20" ref="paypal"></div>
  </div>
</template>

<script lang="ts">
// import image from "../assets/lamp.png"
export default {
  name: "Payment",
  props: ["productChoice"],
  data: function () {
    return {
      loaded: false,
      paidFor: false,
    };
  },
  mounted: function () {
    const script = document.createElement("script");
    script.src = `https://www.paypal.com/sdk/js?client-id=${
      import.meta.env.VITE_CLIENT_ID
    }`;
    script.addEventListener("load", this.setLoaded);
    document.body.appendChild(script);
  },
  methods: {
    setLoaded: function () {
      this.loaded = true;
      window.paypal
        .Buttons({
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.productChoice.title,
                  amount: {
                    currency_code: "USD",
                    value: this.productChoice.price,
                  },
                },
              ],
            });
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture();
            this.paidFor = true;
            console.log(order);
          },
          onError: (err) => {
            console.log(err);
          },
        })
        .render(this.$refs.paypal);
    },
  },
};
</script>
