<template>
  <transition-table id="app"/>
</template>

<script setup>
import TransitionTable from "@/blocks/transactions-table/transactions-table.vue";

</script>

<style lang="scss">
@import '@/styles/variables.scss';

html {
  font-family: "Geologica", sans-serif;
  background-color: $surface-primary;
  color: $primary;
  display: flex;
  justify-content: center;
  padding-top: 6.25rem;
}

body, h2, p, td, th {
  margin: 0;
  padding: 0;
}

p {
  font-size: 0.875rem;
  font-weight: 500;
}

table {
  border-collapse: collapse;
}

::-webkit-scrollbar {
  width: 0.25rem;
}

::-webkit-scrollbar-track {
  background: none;
  width: 0.75rem;
}

::-webkit-scrollbar-thumb {
  background: $surface-4;
  border-radius: 0.375rem;
}

::-webkit-scrollbar-thumb:hover {
  background: $surface-3;
  cursor: pointer;
}

@media (max-width: 768px) {
  html {
    display: block;
  }
}

</style>
