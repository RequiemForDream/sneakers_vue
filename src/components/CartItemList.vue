<script setup>

import CartItem from "@/components/CartItem.vue";
import {inject} from "vue";

const {cart, removeFromCart} = inject('cartActions');

</script>

<template>
  <div class="flex flex-col  flex-1 gap-4 justify-between" v-auto-animate>
    <CartItem v-for="item in cart" v-bind:key="item.id" :title="item.title" :imageUrl="item.imageUrl"
              :price="item.price" @on-click-remove="() => removeFromCart(item)"/>
  </div>
</template>

<style scoped>

</style>