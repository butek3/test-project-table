<template>
  <custom-table
      :columns="columns"
      :data="transitionData"
      :sorts="sorts"
      title="Transitions"
  >
    <template #mobile="{ item }">
      <tr class="mobile-table__main-line text-base" @click="showItems(item)">
        <td>
          <div>
            <p>#{{ item.orderId }}</p>
            <p class="text-secondary">{{ item?.date }}</p>
          </div>
        </td>
        <td class="column-wrapper">
          <div class="end">
            <p class="money">${{ item.resultFinance.balanceAfter.before }}</p>
            <p :class="[ item.resultFinance.sum.isPlus ? 'red' : 'green'  ]"><strong>{{ item?.action }}</strong></p>
          </div>
          <dropdown-icon :class="{ active: !item.isShowItem }" />
        </td>
      </tr>

      <tr :style="{ 'height': 0.5 + 'rem' }">
        <td :colspan="columns.length" />
      </tr>

      <template v-if="item.isShowItem">
        <tr class="mobile-table__line first" :class="{ 'active-line': item.isActive }">
          <td class="mobile-table__column">
            <p class="header-text">#</p>
          </td>
          <td class="text-base">#{{ item.orderId }}</td>
        </tr>
        <tr class="mobile-table__line" :class="{ 'active-line': item.isActive }">
          <td class="mobile-table__column">
            <p class="header-text">
              Date
            </p>
          </td>
          <td>{{ item.date }}</td>
        </tr>
        <tr class="mobile-table__line" :class="{ 'active-line': item.isActive }">
          <td class="mobile-table__column">
            <p class="header-text">
              Action
            </p>
          </td>
          <td>{{ item.action }}</td>
        </tr>
        <tr
            class="mobile-table__line"
            :class="{
                  'item-top': item?.products.length,
                  'active-line': item.isActive
                }"
        >
          <td class="mobile-table__column">
            <p class="header-text">
              Items
            </p>
          </td>
          <td>
            <template v-if="item?.products.length === 1">
              <div class="items-wrapper">
                <div class="image-wrapper" :class="[ item?.isGetItem ? 'plus' : 'minus' ]">
                  <img :src="currentImage(item?.products)" :alt="products?.[0].name" class="image" />
                </div>
                <div>
                  <p>{{ item?.products[0].weaponName }}</p>
                  <p class="text-caption text-xs">{{ item?.products[0].skinName }} ({{ item?.products[0].quality }})</p>
                </div>
              </div>
            </template>

            <template v-else-if="item?.products.length > 1">
              <div class="image-wrapper" :class="[ item?.isGetItem ? 'plus' : 'minus' ]">
                <img :src="currentImage(item?.products)" alt="placeholder" class="image" />
                <div @click="showAllItems(item)">
                  <span class="text-xs" >{{ item?.products.length }} items</span>
                  <button class="text-btn text-xs" >
                    <span class="text"> {{ currentBtnText(item) }} </span>
                    <dropdown-icon :class="{active: !item.isActive}" class="arrow orange" />
                  </button>
                </div>
              </div>
            </template>

            <template v-else>
              <span class="text-base">{{ item?.itemsStatus }}</span>
            </template>
          </td>
        </tr>

        <template v-if="item.isActive">
          <template v-for="(product, idx) in item.products" :key="'product-' + idx">
            <div v-if="idx > 0" class="hr-wrapper">
              <hr>
            </div>
            <tr class="mobile-table__line-all" :class="{ 'first-line': idx === 0 }">
              <td>
                <div class="items-wrapper">
                  <div class="image-wrapper" :class="[ item?.isGetItem ? 'plus' : 'minus' ]">
                    <img :src="product.image" :alt="product.name" class="image" />
                  </div>
                  <div>
                    <p>{{ product.weaponName }}</p>
                    <p class="text-caption text-xs">{{ product.skinName }} ({{ product.quality }})</p>
                  </div>
                </div>
              </td>
            </tr>
            <tr class="mobile-table__line-all">
              <td class="mobile-table__column">
                <p class="header-text">
                  Amount
                </p>
              </td>
              <td class="column-money-wrapper">
                <div class="money-wrapper">
                  <p class="money">${{ item?.resultFinance.balanceAfter.before }}</p>
                  <p class="change-money" :class="[ item.resultFinance.balanceAfter.isPlus ? 'plus-money' : 'minus-money' ]">
                    ${{ product.finance?.amount }}
                  </p>
                </div>
              </td>
            </tr>
            <tr class="mobile-table__line-all">
              <td class="mobile-table__column">
                <p class="header-text">
                  Balance
                </p>
              </td>
              <td class="column-money-wrapper">
                <div class="money-wrapper">
                  <p class="money">${{ product.finance.sum.before }}</p>
                  <p class="change-money" :class="[ item.resultFinance.balanceAfter.isPlus ? 'plus-money' : 'minus-money' ]">
                    ${{ product.finance.sum.change }}
                  </p>
                </div>
              </td>
            </tr>
            <tr class="mobile-table__line-all" :class="{ 'last-line': item.products.length === idx + 1 }">
              <td class="mobile-table__column">
                <p class="header-text">
                  Bonuses
                </p>
              </td>
              <td class="column-money-wrapper">
                <div class="money-wrapper">
                  <p class="money">${{ product.finance.balanceAfter.before }}</p>
                  <p class="change-money" :class="[ item.resultFinance.balanceAfter.isPlus ? 'plus-money' : 'minus-money' ]">
                    ${{ product.finance.balanceAfter.change }}
                  </p>
                </div>
              </td>
            </tr>
          </template>
        </template>

        <tr class="mobile-table__line" :class="{ 'active-line': item.isActive }">
          <td class="mobile-table__column">
            <p class="header-text">
              Amount
            </p>
          </td>
          <td class="money">${{ item.resultFinance?.amount }}</td>
        </tr>
        <tr class="mobile-table__line" :class="{ 'active-line': item.isActive }">
          <td class="mobile-table__column">
            <p class="header-text">
              Balance
            </p>
          </td>
          <td class="money money-wrapper">
            <p class="money text-base">${{ item.resultFinance.sum.before }}</p>
            <p
                class="change-money"
                :class="[ item.resultFinance.sum.isPlus ? 'plus-money' : 'minus-money' ]"
            >
              ${{ item?.resultFinance.sum.change }}
            </p>
          </td>
        </tr>
        <tr class="mobile-table__line last" :class="{ 'active-line': item.isActive }">
          <td class="mobile-table__column">
            <p class="header-text">
              Bonuses
            </p>
          </td>
          <td class="money money-wrapper">
            <p class="money text-base">${{ item.resultFinance.balanceAfter.before }}</p>
            <p
                class="change-money"
                :class="[ item.resultFinance.balanceAfter.isPlus ? 'plus-money' : 'minus-money' ]"
            >
              ${{ item?.resultFinance.balanceAfter.change }}
            </p>
          </td>
        </tr>
      </template>

      <tr v-if="item.isShowItem" :style="{ 'height': 0.5 + 'rem' }">
        <td :colspan="columns.length" />
      </tr>
    </template>

    <template #data:info="{ item }">
      <div>
        <p class="text">#{{ item?.orderId }}</p>
        <span class="text-secondary text-xs">{{ item?.date }}</span>
      </div>
    </template>

    <template #data:action="{ item }">
      <p class="text-secondary text-base">{{ item?.action }}</p>
    </template>

    <template #data:deal="{ item }">
      <template v-if="item?.products.length === 1">
        <div class="items-wrapper">
          <div class="image-wrapper" :class="[ item?.isGetItem ? 'plus' : 'minus' ]">
            <img :src="currentImage(item?.products)" :alt="products?.[0].name" class="image" />
          </div>
          <div>
            <p>{{ item?.products[0].weaponName }}</p>
            <p class="text-caption text-xs">{{ item?.products[0].skinName }} ({{ item?.products[0].quality }})</p>
          </div>
        </div>
      </template>

      <template v-else-if="item?.products.length > 1">
        <div class="image-wrapper" :class="[ item?.isGetItem ? 'plus' : 'minus' ]">
          <img :src="currentImage(item?.products)" alt="placeholder" class="image" />
          <div class="btn-wrapper">
            <span class="text-xs" >{{ item?.products.length }} items</span>
            <button class="btn text-xs" @click="showAllItems(item)">
              <div class="text-btn">{{ currentBtnText(item) }}</div>
              <dropdown-icon class="arrow orange" :class="{active: !item.isActive}"/>
            </button>
          </div>
        </div>
      </template>

      <template v-else>
        <span class="text-base">{{ item?.itemsStatus }}</span>
      </template>
    </template>

    <template #data:amount="{ item }">
      <p class="money text-base">${{ item?.resultFinance?.amount }}</p>
    </template>

    <template #data:balance="{ item }">
      <div class="money-wrapper">
        <p class="money text-base">${{ item?.resultFinance.sum.before }}</p>
        <p class="change-money text-xs" :class="[ item.resultFinance.sum.isPlus ? 'plus-money' : 'minus-money' ]">${{ item?.resultFinance.sum.change }}</p>
      </div>
    </template>

    <template #data:bonuses="{ item }">
      <div class="money-wrapper">
        <p class="money text-base">${{ item?.resultFinance.balanceAfter.before }}</p>
        <p class="change-money text-xs" :class="[ item.resultFinance.balanceAfter.isPlus ? 'plus-money' : 'minus-money' ]">${{ item?.resultFinance.balanceAfter.change }}</p>
      </div>
    </template>

    <template #collapse:deal="{ product }">
      <div class="order-info-wrapper">
        <div class="image-wrapper" :class="[ product?.isGetItem ? 'plus' : 'minus' ]">
          <img :src="product.image" :alt="product.name" class="image" />
        </div>
        <div>
          <p>{{ product?.weaponName }}</p>
          <p class="text-caption text-xs">{{ product?.skinName }} ({{ product?.quality }})</p>
        </div>
      </div>
    </template>

    <template #collapse:amount="{ product }">
      <p class="money text-base">${{ product.finance?.amount }}</p>
    </template>

    <template #collapse:balance="{ product }">
      <div class="money-wrapper">
        <p class="money text-base">${{ product?.finance.sum.before }}</p>
        <p class="change-money text-xs" :class="[ product.finance.sum.isPlus ? 'plus-money' : 'minus-money' ]">${{ product?.finance.sum.change }}</p>
      </div>
    </template>

    <template #collapse:bonuses="{ product }">
      <div class="money-wrapper">
        <p class="money text-base">${{ product?.finance.balanceAfter.before }}</p>
        <p class="change-money text-xs" :class="[ product.finance.balanceAfter.isPlus ? 'plus-money' : 'minus-money' ]">${{ product?.finance.balanceAfter.change }}</p>
      </div>
    </template>
  </custom-table>
</template>

<script setup>
import { ref } from 'vue';

import CustomTable from "@/components/custom-table/custom-table.vue";

import TestImage from '@/assets/icons/test-image.png';
import PlaceholderImage from '@/assets/icons/placeholder-image.png';
import DropdownIcon from '@/assets/svg/dropdown-icon.svg';

const sorts = ref([
  { label: 'All', value: 'all' },
  { label: 'Date', value: 'date' },
  { label: 'Default', value: 'default' }
]);

const columns = ref([
  {
    name: 'Информация',
    key: 'info'
  },
  {
    name: 'Действия',
    key: 'action'
  },
  {
    name: 'Сделка',
    key: 'deal'
  },
  {
    name: 'Сумма',
    key: 'amount'
  },
  {
    name: 'Баланс',
    key: 'balance'
  },
  {
    name: 'Бонус',
    key: 'bonuses'
  }
])

const data = ref([
  {
    action: 'Buy',
    orderId: 111111,
    isGetItem: true,
    date: '12.07.2023 20:54',
    itemsStatus: "products",
    products: [
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isGetItem: true,
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
    ],
    resultFinance: {
      amount: 2500,
      sum: {
        before: 1500,
        change: 111,
        isPlus: false
      },
      balanceAfter: {
        before: 1500,
        change: 111,
        isPlus: true
      },
    },
  },
  {
    action: 'Buy',
    orderId: 111112,
    date: '12.07.2023 20:54',
    itemsStatus: 'Balance',
    products: [],
    resultFinance: {
      amount: 2500,
      sum: {
        before: 1500,
        change: 111,
        isPlus: true
      },
      balanceAfter: {
        before: 1500,
        change: 111,
        isPlus: false
      },
    },
  },
  {
    action: 'Buy',
    orderId: 111113,
    date: '12.07.2023 20:54',
    itemsStatus: 'Bonuses',
    products: [],
    resultFinance: {
      amount: 2500,
      sum: {
        before: 1500,
        change: 111,
        isPlus: true
      },
      balanceAfter: {
        before: 1500,
        change: 111,
        isPlus: false
      },
    },
  },
  {
    action: 'Buy',
    orderId: 111114,
    date: '12.07.2023 20:54',
    itemsStatus: 'products',
    isGetItem: true,
    products: [
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isGetItem: true,
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isGetItem: true,
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isGetItem: true,
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isGetItem: true,
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
    ],
    resultFinance: {
      amount: 2500,
      sum: {
        before: 1500,
        change: 111,
        isPlus: false
      },
      balanceAfter: {
        before: 1500,
        change: 111,
        isPlus: false
      },
    },
  },
  {
    action: 'Auto-sell',
    orderId: 111115,
    date: '12.07.2023 20:54',
    itemsStatus: 'products',
    isGetItem: false,
    products: [
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isStatus: 'buy',
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isGetItem: false,
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isGetItem: false,
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
    ],
    resultFinance: {
      amount: 2500,
      sum: {
        before: 1500,
        change: 111,
        isPlus: true
      },
      balanceAfter: {
        before: 1500,
        change: 111,
        isPlus: false
      },
    },
  },
  {
    action: 'Convert',
    orderId: 111116,
    date: '12.07.2023 20:54',
    itemsStatus: 'products',
    products: [
      {
        image: TestImage,
        weaponName: 'Desert Eagle',
        skinName: 'Ocean Drive',
        quality: 'WW',
        isStatus: 'buy',
        finance: {
          amount: 2500,
          sum: {
            before: 1500,
            change: 111,
            isPlus: true
          },
          balanceAfter: {
            before: 1500,
            change: 111,
            isPlus: false
          },
        },
      },
    ],
    resultFinance: {
      amount: 2500,
      sum: {
        before: 1500,
        change: 111,
        isPlus: true
      },
      balanceAfter: {
        before: 1500,
        change: 111,
        isPlus: false
      },
    },
  },
])
const transitionData = ref([]);

const currentImage = (item) => {
  if (item?.length > 1) {
    return PlaceholderImage
  } else if (item?.length === 0) {
    return null
  } else {
    return item[0].image
  }
}

transitionData.value = data.value.map(item => ({
  ...item,
  isActive: false
}));

const showAllItems = (item) => {
  if (item.products.length > 1) {
    return item.isActive = !item.isActive;
  }
};

const currentBtnText = (item) => {
  return item.isActive ? 'Show less' : 'Show all';
};

const showItems = (item) => {
  item.isActive = false;
  return item.isShowItem = !item.isShowItem;
};

</script>

<style lang="scss" src="./transactions-table.scss" scoped />

<style scoped>
p {
  margin: 0;
}
button {
  font-family: "Geologica", sans-serif;
  border: none;
  padding: 0;
  background: none;
}

button:hover {
  opacity: 0.9;
  cursor: pointer;
}
</style>
