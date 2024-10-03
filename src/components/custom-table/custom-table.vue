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
            <th v-for="({ name, key }, idx) in columns" :key="idx">
              <slot :name="`header:${key}`" :item="name">
                <p class="header-text">{{ name }}</p>
              </slot>
            </th>
          </tr>

          <tr :style="{ 'height': computedLineSpace + 'rem' }">
            <td :colspan="columns.length" />
          </tr>
        </thead>

        <tbody v-if="isMobile" class="mobile-table text-base">
          <template v-for="(item, index) in items" :key="index">
            <slot name="mobile" :item="item" />
          </template>
        </tbody>

        <tbody v-else>
          <template v-for="(item, index) in data" :key="index">
            <tr class="custom-table__line" :class="{ 'active-line': item.isActive } ">
              <slot name="row" :item="item" :index="index">
                <td v-for="{ key } in columns" :key="`data:${ key }`">
                  <slot :name="`data:${ key }`" :item="item" :index="index">
                    {{ item[key] }}
                  </slot>
                </td>
              </slot>
            </tr>

            <tr v-if="!item.isActive && data.length !== index+1" :style="{ 'height': computedLineSpace + 'rem' }">
              <td :colspan="columns.length" />
            </tr>

            <template v-if="item.isActive">
              <tr
                v-for="(product, idx) in item?.products"
                class="custom-table__line-all"
                :class="{
                  'last-line': item.products.length === idx+1,
                  'first-line': idx === 0
                }"
                :key="'product-' + idx"
              >
                <slot name="collapse-row" :product="product">
                  <td v-for="{ key } in columns" :key="`collapse:${ key }`">
                    <slot :name="`collapse:${ key }`" :product="product">
                      {{ product[key] }}
                    </slot>
                  </td>
                </slot>
              </tr>

              <tr :style="{ 'height': computedLineSpace + 'rem' }">
                <td :colspan="columns.length" />
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
  columns: {
    type: Array,
    default: () => []
  },
  data: {
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

const { data } = toRefs(props)

const items = ref([])

items.value = data.value.map(item => ({
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