<script setup>
import { computed, onMounted, ref } from 'vue'

const items = ref([])

onMounted(() => {
  const storage = localStorage.getItem('items')

  if (storage) {
    items.value = JSON.parse(localStorage.getItem('items'))
  }
})

const updateLocalStorage = () => {
  localStorage.setItem('items', JSON.stringify(items.value))
}

const totalPrice = computed(() => {
  return items.value
    .reduce((total, item) => {
      const price = item.price ?? 0
      return total + price * item.quantity
    }, 0)
    .toFixed(2)
})

const totalSupplementPrice = computed(() => {
  return totalPrice.value > 25 ? (totalPrice.value - 25).toFixed(2) : 0
})

const itemName = ref()

const addItem = () => {
  if (!itemName.value.value) {
    return
  }

  items.value.push({
    id: items.value.length + 1,
    name: itemName.value.value,
    price: undefined,
    quantity: 1
  })

  itemName.value.value = ''
  updateLocalStorage()
}

const findItemById = (id) => {
  return items.value.find((item) => item.id === id)
}

const removeItem = (itemId) => {
  const item = findItemById(itemId)

  if (!item) {
    return
  }

  if (confirm(`Supprimer ${item.name} ?`)) {
    items.value = items.value.filter((listItem) => listItem.id !== item.id)
  }

  updateLocalStorage()
}

const changePrice = (itemId) => {
  const item = findItemById(itemId)

  const newPrice = prompt(`Prix de ${item.name}`, item.price)

  if (!newPrice) {
    return
  }

  const formattedNewPrice = newPrice.replace(',', '.')

  if (newPrice && !isNaN(formattedNewPrice)) {
    const index = items.value.findIndex((item) => item.id === itemId)
    items.value[index].price = parseFloat(formattedNewPrice).toFixed(2)
  }

  updateLocalStorage()
}

const changeQuantity = (itemId) => {
  const item = findItemById(itemId)

  const newQuantity = prompt(`Quantité de ${item.name}`, item.quantity)

  if (newQuantity && !isNaN(newQuantity)) {
    const index = items.value.findIndex((item) => item.id === itemId)
    items.value[index].quantity = newQuantity
  }

  updateLocalStorage()
}

const clear = () => {
  const confirmation = confirm('Tout effacer ?')

  if (confirmation) {
    items.value = []
  }

  updateLocalStorage()
}
</script>

<template>
  <div class="flex flex-col h-full p-3 max-w-[576px] mx-auto">
    <h1 class="text-xl font-bold mb-4 flex items-center">
      <svg height="20" viewBox="0 0 59 64" xmlns="http://www.w3.org/2000/svg" class="me-2">
        <g fill="#2787f5">
          <path
            d="m18.2363636 51.2c-3.5547054 0-6.4363636 2.8653776-6.4363636 6.4s2.8816582 6.4 6.4363636 6.4c3.5547055 0 6.4363637-2.8653776 6.4363637-6.4s-2.8816582-6.4-6.4363637-6.4z"
          />
          <path
            d="m38.6181818 51.2c-3.5547055 0-6.4363636 2.8653776-6.4363636 6.4s2.8816581 6.4 6.4363636 6.4 6.4363637-2.8653776 6.4363637-6.4-2.8816582-6.4-6.4363637-6.4z"
          />
          <path
            d="m49.7829077 3.21270393c.9857402-2.49784341 3.8115289-3.87823055 6.3744534-2.89223973l-.4008676-.27607743c2.4972085.98599081 3.8772448 3.81249782 2.9572206 6.37607395l-12.3480392 33.99039008c-1.1828883 3.2866361-4.271541 5.3900831-7.6887737 5.4558159h-23.4606174c-3.2858008 0-6.24302143-1.9719817-7.49162572-4.9299541l-.00596503-.0146969c-.1187215-.3072195-1.86336072-5.1746065-3.60020129-10.0341929l-.273935-.7665723c-1.41253469-3.9534068-2.76265514-7.7416201-3.16338723-8.8846075l-.06546786-.1875265c-.03542432-.102109-.05408923-.157513-.05408923-.1608839-.98574022-2.4978434-.65716015-5.2586177.8543082-7.4277974l.16007719-.2349226c1.52845713-2.1636574 4.01175673-3.50957591 6.74010438-3.44610983h37.85242469c.7228761 0 1.3143203-.46012905 1.5771843-1.05172354zm-12.7738168 31.98729607c-1.4811273 0-2.6818182 1.1939073-2.6818182 2.6666667 0 1.4727593 1.2006909 2.6666666 2.6818182 2.6666666s2.6818182-1.1939073 2.6818182-2.6666666c0-1.4727594-1.2006909-2.6666667-2.6818182-2.6666667zm-9.5290135 0-9.6916399.0092091c-.070549-.0060989-.141911-.0092091-.2139694-.0092091-1.4117979 0-2.5562863 1.1939073-2.5562863 2.6666667 0 1.4727593 1.1444884 2.6666666 2.5562863 2.6666666.0720584 0 .1434204-.0031102.2130238-.0033333l9.5948824.0014215.0977031.0019118c1.4117979 0 2.5562862-1.1939073 2.5562862-2.6666666 0-1.4161148-1.0581438-2.5744152-2.3946227-2.6614204zm-9.7800774-9.6c-1.4811273 0-2.6818182 1.1939073-2.6818182 2.6666667 0 1.4727593 1.2006909 2.6666666 2.6818182 2.6666666s2.6818182-1.1939073 2.6818182-2.6666666c0-1.4727594-1.2006909-2.6666667-2.6818182-2.6666667zm9.6545455 0c-1.4811273 0-2.6818182 1.1939073-2.6818182 2.6666667 0 1.4727593 1.2006909 2.6666666 2.6818182 2.6666666 1.4811272 0 2.6818181-1.1939073 2.6818181-2.6666666 0-1.4727594-1.2006909-2.6666667-2.6818181-2.6666667zm9.6545454 0c-1.4811273 0-2.6818182 1.1939073-2.6818182 2.6666667 0 1.4727593 1.2006909 2.6666666 2.6818182 2.6666666s2.6818182-1.1939073 2.6818182-2.6666666c0-1.4727594-1.2006909-2.6666667-2.6818182-2.6666667zm-19.3090909-9.6c-1.4811273 0-2.6818182 1.1939073-2.6818182 2.6666667 0 1.4727593 1.2006909 2.6666666 2.6818182 2.6666666s2.6818182-1.1939073 2.6818182-2.6666666c0-1.4727594-1.2006909-2.6666667-2.6818182-2.6666667zm9.6545455 0c-1.4811273 0-2.6818182 1.1939073-2.6818182 2.6666667 0 1.4727593 1.2006909 2.6666666 2.6818182 2.6666666 1.4811272 0 2.6818181-1.1939073 2.6818181-2.6666666 0-1.4727594-1.2006909-2.6666667-2.6818181-2.6666667zm9.6545454 0c-1.4811273 0-2.6818182 1.1939073-2.6818182 2.6666667 0 1.4727593 1.2006909 2.6666666 2.6818182 2.6666666s2.6818182-1.1939073 2.6818182-2.6666666c0-1.4727594-1.2006909-2.6666667-2.6818182-2.6666667z"
          />
        </g>
      </svg>
      SpendSmart
    </h1>
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
            <td
              @click="changePrice(item.id)"
              class="p-2 text-right whitespace-nowrap pe-2 bg-gray-50"
            >
              <template v-if="item.price">
                <span class="w-[48px] text-right pe-1">{{ item.price }}</span>
                &euro;
              </template>
              <template v-else> Non défini </template>
            </td>
            <td @click="changeQuantity(item.id)" class="bg-gray-50 p-2 text-right">
              {{ item.quantity }}
            </td>
          </tr>
        </template>
        <template v-else>
          <tr>
            <td class="py-2 text-center text-gray-500 flex flex-col items-center h-36" colspan="3">
              <svg
                height="80"
                viewBox="0 0 64 64"
                width="80"
                xmlns="http://www.w3.org/2000/svg"
                class="mb-2"
              >
                <g fill="none" fill-rule="evenodd">
                  <circle cx="32" cy="32" fill="#e8f4fa" fill-rule="nonzero" r="32" />
                  <g transform="translate(15.6468 12.912)">
                    <g fill-rule="nonzero">
                      <ellipse
                        cx="17.809246"
                        cy="41.552019"
                        fill="#191816"
                        opacity=".15"
                        rx="13.8"
                        ry="1.376"
                      />
                      <path
                        d="m2.20924575 9.93601901c-.70066855-.00038926-1.35993427.33180249-1.77653856.89516509-.41660429.5633626-.54109257 1.29102-.33546144 1.9608353l3.064 9.936c.26706222.9421682 1.12472895 1.5943863 2.104 1.6h20.46400005c-.0112729-.0769226-.0112729-.1550775 0-.232l3.024-14.16000039z"
                        fill="#ff6242"
                      />
                      <path
                        d="m.08924575 11.5920194h28.31200005l.352-1.65600005h-26.54400005c-1.00212703.00249565-1.87495668.68429085-2.12 1.65600005z"
                        fill="#ff866e"
                      />
                      <path
                        d="m10.2652458 33.1200194c0 1.2194447-.98855532 2.208-2.20800005 2.208s-2.208-.9885553-2.208-2.208c0-1.2194448.98855527-2.208 2.208-2.208 1.21762258.0043878 2.20361215.9903774 2.20800005 2.208z"
                        fill="#8ca4b8"
                        stroke="#191816"
                        stroke-width=".8"
                      />
                      <path
                        d="m29.0332458 33.1200194c0 1.2194447-.9885553 2.208-2.208 2.208-1.2194448 0-2.208-.9885553-2.208-2.208 0-1.2194448.9885552-2.208 2.208-2.208 1.2176225.0043878 2.2036121.9903774 2.208 2.208z"
                        fill="#8ca4b8"
                        stroke="#191816"
                        stroke-width=".8"
                      />
                    </g>
                    <path
                      d="m2.20924575 9.93601901c-.70066855-.00038926-1.35993427.33180249-1.77653856.89516509-.41660429.5633626-.54109257 1.29102-.33546144 1.9608353l3.064 9.936c.26706222.9421682 1.12472895 1.5943863 2.104 1.6h20.46400005c-.0112729-.0769226-.0112729-.1550775 0-.232l3.024-14.16000039z"
                      stroke="#191816"
                      stroke-linejoin="round"
                      stroke-width=".8"
                    />
                    <path
                      d="m30.1372458 3.31201935h6.072c.5916318 0 1.1383222-.31563179 1.4341381-.82799998s.2958159-1.14363185 0-1.65600004-.8425063-.8280146-1.4341381-.8280146h-3.856c-.5862502-.0021211-1.1491054.22982091-1.5636496.6443651-.4145442.4145442-.6464862.97739939-.6443504 1.56364952z"
                      fill="#ff6242"
                      fill-rule="nonzero"
                    />
                    <path
                      d="m36.2092458.00001935h-3.856c-1.0061417.00085988-1.8844649.68180711-2.136 1.656h7.656c0-.44058812-.1755636-.86301512-.4878562-1.17380636-.3122927-.31079123-.7355609-.48432208-1.1761438-.48219364z"
                      fill="#ff866e"
                      fill-rule="nonzero"
                    />
                    <path
                      d="m29.5212458 24.9760194-1.472-1.136 4.376-20.52800005h-2.256l-4.424 20.74400005c-.0919665.4161477.0675102.8476729.408 1.104l2.024 1.6c.3842806.2784872.5376654.7778037.376 1.224-.1448464.45466-.5708984.7603766-1.048.752h-22.76000005c-.60972236 0-1.104.4942776-1.104 1.104 0 .6097223.49427764 1.104 1.104 1.104h22.76000005c1.4169061-.002087 2.6755408-.9052717 3.1311935-2.2469155.4556526-1.3416438.0073822-2.824532-1.1151935-3.6890845z"
                      fill="#c0dceb"
                      fill-rule="nonzero"
                      stroke="#191816"
                      stroke-linejoin="round"
                      stroke-width=".8"
                    />
                    <path
                      d="m30.1372458 3.31201935h6.072c.5916318 0 1.1383222-.31563179 1.4341381-.82799998s.2958159-1.14363185 0-1.65600004-.8425063-.8280146-1.4341381-.8280146h-3.856c-.5862502-.0021211-1.1491054.22982091-1.5636496.6443651-.4145442.4145442-.6464862.97739939-.6443504 1.56364952z"
                      stroke="#191816"
                      stroke-linejoin="round"
                      stroke-width=".8"
                    />
                    <path
                      d="m22.961246 20.976019h-17.112"
                      stroke="#191816"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width=".8"
                    />
                    <path
                      d="m23.785246 17.664019h-19.04"
                      stroke="#191816"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width=".8"
                    />
                    <path
                      d="m24.617246 14.352019h-20.976"
                      stroke="#191816"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width=".8"
                    />
                  </g>
                </g>
              </svg>
              Aucun produit ajouté
            </td>
          </tr>
        </template>
      </tbody>
    </table>
    <div class="py-2 relative flex items-center">
      <input
        @keyup.enter="addItem"
        ref="itemName"
        type="text"
        value=""
        class="border-solid border border-gray-400 rounded py-1.5 px-3 w-full"
        placeholder="Nom du produit à ajouter"
      />
      <button
        type="button"
        @click="addItem"
        class="absolute m-1 right-0.5 bg-blue-600 hover:bg-blue-700 text-white px-2 text-sm py-1 rounded"
      >
        Ajouter
      </button>
    </div>
    <div class="mt-auto">
      <div class="m-4 text-center">
        <span class="text-3xl text-center flex justify-center">{{ totalPrice }} &euro;</span>
        <span class="text-xs" v-show="totalSupplementPrice"
          >{{ totalSupplementPrice }} &euro; en supplément</span
        >
      </div>
      <button
        @click="clear"
        type="button"
        class="bg-red-800 hover:bg-red-900 text-white py-1 px-4 text-sm w-full mt-auto text-center rounded"
      >
        Effacer
      </button>
    </div>
  </div>
</template>
