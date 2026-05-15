<script setup>


import {onMounted, ref} from "vue";
import axios from "axios";
import CardList from "@/components/CardList.vue";

const favorites = ref([]);

onMounted(async () => {
  try {
    const {data} = await axios.get(`https://3d7654aaeceb4b56.mokky.dev/favorites?_relations=items`);

    favorites.value = data.map((obj) => obj.item);
    console.log(data);
  }
  catch(err) {

  }
})
</script>

<template>
  <h2>Мои закладки</h2>

  <div class="mt-10">
  <CardList :items="favorites"/>
  </div>
</template>

<style scoped>

</style>