<template>
  <transition-table id="app"/>
</template>

<script setup>
import TransitionTable from "@/blocks/transactions-table/transactions-table.vue";

</script>

<style>

html {
  font-family: "Geologica", sans-serif;
  background-color: #171820;
  color: #FCFCFD;
  display: flex;
  justify-content: center;
  padding-top: 100px;
}

body, h2, p, td {
  margin: 0;
  padding: 0;
}

p {
  font-size: 14px;
  font-weight: 500;
}

table {
  border-collapse: collapse;
}

::-webkit-scrollbar {
  width: 4px;
}

::-webkit-scrollbar-track {
  background: none;
  width: 12px;
}

::-webkit-scrollbar-thumb {
  background: #2A2C3B;
  border-radius: 6px;
}

::-webkit-scrollbar-thumb:hover {
  background: #21232F;
  cursor: pointer;
}

@media (max-width: 768px) {
  html {
    display: block;
  }
}

</style>
