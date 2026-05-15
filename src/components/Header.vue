<script setup lang="ts">

defineProps({
  totalPrice: Number,
})

const emit = defineEmits<{
  (e: 'setDrawerActive', isActive: boolean): void
}>()

</script>

<template>
  <header class="flex justify-between border-b border-slate-200 px-10 py-8">

    <router-link to="/">
      <div class="flex items-center gap-4">
        <img alt="Logo" src="/logo.png" class="w-10">
        <div>
          <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
          <p class="text-slate-400">Магазин лучших кроссовок</p>
        </div>
      </div>
    </router-link>
   

    <ul class="flex items-center gap-10">
      <li class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black"
          @click="$emit('setDrawerActive', true)">
        <img src="/cart.svg" alt="Cart"/>
        <b>{{ totalPrice }} руб.</b>
      </li>

      <router-link to="/favorites">
        <li class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black">
          <img src="/heart.svg" alt="Heart"/>
          <span>Закладки</span>
        </li>
      </router-link>


      <li class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black">
        <img src="/profile.svg" alt="Profile"/>
        <span>Профиль</span>
      </li>
    </ul>

  </header>
</template>

<style scoped>

</style>