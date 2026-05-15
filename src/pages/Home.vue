<script setup>

import CardList from "@/components/CardList.vue";
import {inject, onMounted, reactive, ref, watch} from "vue";
import debounce from "lodash.debounce";
import axios from "axios";

const { cart, addToCart, removeFromCart} = inject('cartActions', );

const items = ref([]);

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
});

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
  console.log(event.target.value);
};

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value
}, 500 );

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
};

const addToFavorite = async (item) => {
  console.log(item);
  try {
    item.isFavorite = !item.isFavorite;

    if (item.isFavorite) {
      const obj = {
        item_id: item.id,
      }

      const {data} = await axios.post(`https://3d7654aaeceb4b56.mokky.dev/favorites/`, obj);

      item.favoriteId = data.id;
    } else {
      await axios.delete(`https://3d7654aaeceb4b56.mokky.dev/favorites/${item.favoriteId}`);
      item.favoriteId = null;
    }
  } catch (e) {
    console.log(e);
  }
}

async function fetchFavorites() {
  try {

    const {data: favorites} = await axios.get(`https://3d7654aaeceb4b56.mokky.dev/favorites`);

    items.value = items.value.map(item => {
      const favorite = favorites.find(favorite => favorite.item_id === item.id);

      if (!favorite) {
        return item;
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      }
    });


  } catch (e) {
    console.log("NO items");
  }
}


const fetchItems = async () => {
  try {

    const params = {
      sortBy: filters.sortBy,
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`;
    }

    const {data} = await axios.get('https://3d7654aaeceb4b56.mokky.dev/items',
        {
          params: params,
        }
    );

    items.value = data.map(obj => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false,
    }));
  } catch (e) {
    console.log("NO items");
  }
}

watch(filters, fetchItems);

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }))
})

onMounted(async () => {
  const localCart = localStorage.getItem('cart');

  cart.value = localCart ? JSON.parse(localCart) : [];
  await fetchItems()
  await fetchFavorites()
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id),
  }))

});
</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене (дешевые)</option>
        <option value="-price">По цене (дорогие)</option>
      </select>

      <div class="relative">
        <img class="absolute left-3 top-3" src="/search.svg" alt="search"/>
        <input
            @input="onChangeSearchInput"
            placeholder="Поиск..." class="border rounded-md py-2 pl-11 pr-4 outline-none border-gray-400"
            type="text"
        />
      </div>
    </div>
  </div>

  <div class="mt-10">
    <CardList :items="items" @addToFavorite="addToFavorite" @add-to-cart="onClickAddPlus"/>
  </div>
</template>

<style scoped>

</style>