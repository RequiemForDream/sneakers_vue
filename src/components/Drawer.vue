<script setup lang="ts">

import DrawerHead from "@/components/DrawerHead.vue";
import CartItemList from "@/components/CartItemList.vue";
import {computed, inject, ref} from "vue";
import InfoBlock from "@/components/InfoBlock.vue";
import axios from "axios";

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
});

const emit = defineEmits<{
  (e: 'setDrawerActive', isActive: boolean): void,
  (e: 'createOrder'): void
}>()

const {cart} = inject('cartActions');

const buttonDisabled = computed(() => isCreating.value || cart.value.length === 0)

const isCreating = ref(false);
const orderId = ref(null);

const createOrder = async () => {
  try {
    isCreating.value = true;
    const {data} = await axios.post(`https://3d7654aaeceb4b56.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: props.totalPrice,
    });

    cart.value = [];
    orderId.value = data.id;
    return data;
  } catch (e) {
    console.error(e);
  } finally {
    isCreating.value = false;
  }
}

</script>

<template>
  <div @click="$emit('setDrawerActive', false)" class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead @setDrawerActive="$emit('setDrawerActive', false)"/>


    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlock
          v-if="!totalPrice && !orderId"
          title="Корзина Пустая" description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
          imageUrl="/package-icon.png"/>
      <InfoBlock
          v-if="orderId"
          title="Заказ оформлен!"
          :description="`Ваш заказ №${orderId} скоро будет передан в доставку`"
          imageUrl="/order-success-icon.png"/>
    </div>

    <div v-if="!totalPrice" class="flex h-full items-center">

    </div>


    <CartItemList v-if="totalPrice"/>

    <div v-if="totalPrice" class="flex flex-col gap-4 my-6 mt-7">
      <div class="flex gap-2">
        <span>Итого:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>{{ totalPrice }} ₽</b>
      </div>

      <div class="flex gap-2">
        <span>Налог 5%:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>{{ vatPrice }} ₽</b>
      </div>

      <button @click="createOrder"
              :disabled="buttonDisabled"
              class=" mt-4 transition bg-lime-500 w-full rounded-xl py-3 text-white
           disabled:bg-slate-300 cursor-pointer hover:bg-lime-600 active:bg-lime-700">Оформить заказ
      </button>

    </div>
  </div>
</template>