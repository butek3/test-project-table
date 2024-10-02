<template>
  <div class="custom-table">
    <div v-if="title && sorts" class="custom-table__filter">
      <h2 class="text-xl">{{ title }}</h2>

      <custom-select :options="sorts" />
    </div>

    <div class="custom-table__scroll-container">
      <table class="custom-table__wrapper">
        <thead>
          <tr class="custom-table__header-line">
            <th v-for="(header, idx) in headers" :key="idx">
              <slot :name="header" :item="header" />
            </th>
          </tr>

          <tr :style="{ 'height': computedLineSpace + 'rem' }">
            <td :colspan="headers.length" />
          </tr>
        </thead>

        <tbody v-if="isMobile" class="mobile-table text-base">
          <template v-for="(item, index) in items" :key="index">
            <slot name="mobile" :item="item" />
          </template>
        </tbody>

        <tbody v-else>
          <template v-for="(item, index) in transitionData" :key="index">
            <tr class="custom-table__line" :class="{ 'active-line': item.isActive } ">
              <slot name="row" :item="item" :index="index" />
            </tr>

            <tr v-if="!item.isActive && transitionData.length !== index+1" :style="{ 'height': computedLineSpace + 'rem' }">
              <td :colspan="headers.length" />
            </tr>

            <template v-if="item.isActive">
              <tr
                  v-for="(product, idx) in item?.products"
                  class="custom-table__line-all"
                  :class="{ 'last-line': item.products.length === idx+1 }"
                  :key="'product-' + idx"
              >
                <slot name="product-row" :product="product" />
              </tr>

              <tr :style="{ 'height': computedLineSpace + 'rem' }">
                <td :colspan="headers.length" />
              </tr>
            </template>
          </template>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { defineProps, ref, toRefs } from 'vue';

import CustomSelect from '@/components/custom-select/custom-select.vue';

const props = defineProps({
  headers: {
    type: Array,
    default: () => []
  },
  transitionData: {
    type: Array,
    default: () => []
  },
  sorts: {
    type: Array,
    default: () => []
  },
  title: {
    type: String,
    default: '',
  },
  lineSpace: {
    type: Number,
    default: 8,
  }
});

const { transitionData } = toRefs(props)

const items = ref([])

items.value = transitionData.value.map(item => ({
  ...item,
  isShowItem: false
}));

const computedLineSpace = props.lineSpace / 16;

const isMobile = ref(window.innerWidth <= 768);

window.addEventListener('resize', () => {
  isMobile.value = window.innerWidth <= 768;
});

</script>

<style lang="scss" src="./custom-table.scss" scoped />

<style lang="scss" scoped>
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