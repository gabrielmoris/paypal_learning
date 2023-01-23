<template>
  <div class="flex flex-col justify-center items-center">
    <h1 class="font-bold text-[4rem]">
      <span class="text-blue-900">Pay</span
      ><span class="text-blue-500">Pal</span> playground
    </h1>

    <div v-if="JSON.stringify(selected) === '{}'" class="grid grid-cols-3">
      <div
        v-for="item in items"
        class="m-5 p-5 flex flex-col cursor-pointer border rounded-xl border-gray-400 hover:shadow-xl hover:bottom-1 hover:right-1 hover:relative"
        :id="item.id"
        @click.prevent="click(item.id)"
      >
        <h1 class="text-bold text-xl text-center">{{ item.title }}</h1>
        <div
          class="h-[400px] rounded-t-xl"
          :style="`background-image: url(${item.image}); background-position: center;background-size: contain; background-repeat: no-repeat;`"
          :title="item.title"
        ></div>
        <div class="rounded-b-xl bg-white p-4 flex flex-col justify-between">
          <div class="mb-8">
            <div class="text-gray-900 font-bold text-xl mb-2">
              {{ item.price }}€
            </div>
            <p class="text-gray-700 text-base">
              {{ item.description }}
            </p>
          </div>
        </div>
      </div>
    </div>
    <Payment v-else :productChoice="selected" />
  </div>
</template>

<script lang="ts">
import Payment from "./components/Payment.vue";

export default {
  data() {
    return {
      items: [],
      selected: {},
    };
  },
  components: { Payment },
  methods: {
    click(id: number) {
      fetch(`https://fakestoreapi.com/products/${id}`)
        .then((res) => res.json())
        .then((json) => {
          this.selected = json;
          console.log(json);
        });
    },
    api() {
      fetch("https://fakestoreapi.com/products?limit=15")
        .then((res) => res.json())
        .then((json) => {
          this.items = json;
          // console.log(this.items);
        });
    },
  },
  mounted() {
    if (JSON.stringify(this.selected) === "{}") {
      this.api();
    }
  },
};
</script>
