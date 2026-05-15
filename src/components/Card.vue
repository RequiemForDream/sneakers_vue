<script setup>

defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function,
});

</script>

<template>
  <div class="border-slate-200 border rounded-3xl ">
    <div
        class="relative bg-white rounded-3xl p-8 transition cursor-pointer hover:-translate-y-3 hover:shadow-xl h-full">
      <img
          v-if="onClickFavorite"
          @click="onClickFavorite" :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'" alt="Like 2"
           class="absolute top-0 left-0"/>
      <img :src="imageUrl" alt="Sneaker"/>

      <p class="mt-2">{{ title }}</p>

      <div class="flex justify-between mt-5">
        <div class="flex flex-col">
          <span class="text-slate-400">Цена:</span>
          <span> {{ price }} руб.</span>
        </div>

        <img v-if="onClickAdd" @click="onClickAdd" :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="Plus"/>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>