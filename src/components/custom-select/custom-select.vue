<template>
  <div class="custom-select text-secondary text-base">
    <div class="selected" @click="toggleDropdown">
      <div>
        <sort-icon class="selected-image" :class="{ active: isOpen }" />
        <span>{{ selectedLabel }}</span>
      </div>
      <dropdown-icon class="icon-arrow" :class="{ active: !isOpen }" />
    </div>

    <div v-if="isOpen" class="dropdown">
      <ul>
        <li v-for="(option, index) in options" :key="index" @click="selectOption(option)">
          <span>{{ option.label }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, defineProps, computed } from 'vue';

import SortIcon from '@/assets/svg/sorts-icon.svg'
import DropdownIcon from '@/assets/svg/dropdown-icon.svg'

const props = defineProps({
  options: {
    type: Array,
    required: true
  }
});

const selectedOption = ref(props.options?.[0]);
const isOpen = ref(false);

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};

const selectOption = (option) => {
  selectedOption.value = option;
  isOpen.value = false;
};

const selectedLabel = computed(() => selectedOption.value?.label);
</script>

<style lang="scss" scoped>
@import '@/styles/variables.scss';

.custom-select {
  position: relative;
  width: 8.625rem;
}

.selected {
  display: flex;
  align-items: center;
  justify-content: space-between;
  cursor: pointer;
  background-color: $surface-4;
  padding: 0.625rem;
  border-radius: 0.375rem;
}

.selected-image {
  width: 1rem;
  height: 1rem;
  margin-right: 0.5rem;
}

.dropdown {
  border-radius: 0 0 0.375rem 0.375rem;
  position: absolute;
  top: 90%;
  left: 0;
  right: 0;
  background-color: $surface-4;
  z-index: 10;
}

.dropdown {
  ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
  }
}

.dropdown {
  li {
    padding: 1rem;
    display: flex;
    align-items: center;
    cursor: pointer;
  }
}

.icon-arrow {
  height: auto;
  margin-right: 0.5rem;
}

.active {
  transform: rotateX(180deg);
}

.dropdown {
  li:hover {
    opacity: 0.9;
  }
}
</style>