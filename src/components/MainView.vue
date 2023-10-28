<script setup>
import {computed, onMounted, ref} from "vue";

const items = ref([]);

onMounted(() => {
  const storage = localStorage.getItem('items');

  if (storage) {
    items.value = JSON.parse(localStorage.getItem('items'));
  }
});

const updateLocalStorage = () => {
  localStorage.setItem('items', JSON.stringify(items.value));
};

const totalPrice = computed(() => {
  return items.value.reduce((total, item) => {
    const price = item.price ?? 0;
    return total + (price * item.quantity);
  }, 0).toFixed(2);
});

const totalSupplementPrice = computed(() => {
  return totalPrice.value > 25
    ? (totalPrice.value - 25).toFixed(2)
    : 0;
});

const itemName = ref();

const addItem = () => {
  items.value.push({
    id: items.value.length + 1,
    name: itemName.value.value,
    price: undefined,
    quantity: 1,
  });

  itemName.value.value = '';
  updateLocalStorage();
};

const findItemById = (id) => {
  return items.value.find((item) => item.id === id);
};

const removeItem = (itemId) => {
  const item = findItemById(itemId);

  if (!item) {
    return;
  }

  if (confirm(`Supprimer ${item.name} ?`)) {
    items.value = items.value.filter((listItem) => listItem.id !== item.id);
  }

  updateLocalStorage();
};

const changePrice = (itemId) => {
  const item = findItemById(itemId);

  const newPrice = prompt(`Prix de ${item.name}`, item.price);

  if (!newPrice) {
    return;
  }

  const formattedNewPrice = newPrice.replace(',', '.');

  if (newPrice && !isNaN(formattedNewPrice)) {
    const index = items.value.findIndex((item) => item.id === itemId);
    items.value[index].price = newPrice;
  }

  updateLocalStorage();
}

const changeQuantity = (itemId) => {
  const item = findItemById(itemId);

  const newQuantity = prompt(`Quantité de ${item.name}`, item.quantity);

  if (newQuantity && !isNaN(newQuantity)) {
    const index = items.value.findIndex((item) => item.id === itemId);
    items.value[index].quantity = newQuantity;
  }

  updateLocalStorage();
}

const clear = () => {
  const confirmation = confirm('Tout effacer ?');

  if (confirmation) {
    items.value = [];
  }

  updateLocalStorage();
}
</script>

<template>
  <div class="flex flex-col h-full p-3 max-w-[576px] mx-auto">
    <h1 class="text-xl font-bold mb-4">Courses</h1>
    <table>
      <template v-if="items.length">
        <thead>
          <tr>
            <th class="text-left">Produit</th>
            <th class="text-right pe-2">Prix</th>
            <th class="text-right">Quantité</th>
          </tr>
        </thead>
      </template>
      <tbody>
        <template v-if="items.length">
          <tr v-for="item in items" :key="item.id" class="border-t border-gray-100">
            <td class="bg-gray-50 p-2" @click="removeItem(item.id)">{{ item.name }}</td>
            <td @click="changePrice(item.id)" class="p-2 text-right whitespace-nowrap pe-2 bg-gray-50">
              <template v-if="item.price">
                <span class="w-[48px] text-right pe-1">{{ item.price }}</span>
                &euro;
              </template>
              <template v-else>
                Non défini
              </template>
            </td>
            <td @click="changeQuantity(item.id)" class="bg-gray-50 p-2 text-right">{{ item.quantity }}</td>
          </tr>
        </template>
        <template v-else>
          <tr>
            <td class="py-2 text-center" colspan="3">Aucun produit ajouté</td>
          </tr>
        </template>
        <tr>
          <td class="py-2" colspan="3">
            <input @keyup.enter="addItem" ref="itemName" type="text" value="" class="border-solid border border-gray-400 rounded py-1.5 px-3 w-full" placeholder="Nom du produit à ajouter">
          </td>
        </tr>
      </tbody>
    </table>
    <div class="mt-auto">
      <div class="m-4 text-center">
        <span class="text-3xl text-center flex justify-center">{{ totalPrice }} &euro;</span>
        <span class="text-xs" v-show="totalSupplementPrice">{{ totalSupplementPrice }} &euro; en supplément</span>
      </div>
      <button @click="clear" type="button" class="bg-red-800 text-white py-1 px-4 text-sm w-full mt-auto text-center rounded">Effacer</button>
    </div>
  </div>
</template>
