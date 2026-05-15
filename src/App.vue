<script setup lang="ts">
import "tailwindcss";
import {computed, onMounted, provide, ref, watch} from "vue";
import axios from "axios";

import Header from "@/components/Header.vue";
import Drawer from "@/components/Drawer.vue";
import Home from "@/pages/Home.vue";



const drawerOpen = ref<boolean>(false);

const cart = ref([]);


const totalPrice = computed(
    () => cart.value.reduce((acc, item) => acc + item.price, 0)
);

const vatPrice = computed(() => Math.round(totalPrice.value * 5 / 100));


function setDrawerActive(isActive: boolean) {
  drawerOpen.value = isActive;
}





const addToCart = (item) => {
  cart.value.push(item);
  item.isAdded = true;
};

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1);
  item.isAdded = false;
};




watch(cart, () => {
  localStorage.setItem('cart', JSON.stringify(cart.value));
}, {deep: true});




provide('cartActions', {
  cart,
  closeDrawer: () => setDrawerActive(false),
  openDrawer: () => setDrawerActive(true),
  addToCart,
  removeFromCart,
});

</script>

<template>
  <Drawer @set-drawer-active="setDrawerActive" v-if="drawerOpen" :totalPrice="totalPrice" :vatPrice="vatPrice"/>
  <div class=" bg-white w-4/5 m-auto border-radius rounded-xl shadow-xl mt-14">
    <Header :totalPrice="totalPrice" @set-drawer-active="setDrawerActive"/>
    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>
</template>


<style>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type=number] {
  -moz-appearance: textfield;
}
</style>
