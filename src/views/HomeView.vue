<script setup lang="ts">
import SimpleFilters from '../components/components/filter/SimpleFilters.vue'
import HeaderItem from '../components/components/etc/HeaderItem.vue'
import TableItem from '../components/components/main/TableItem.vue'
import UIFilters from '../components/components/filter/UIFilters.vue'
import Skip from '../components/components/main/Skip.vue';
</script>

<template>
  <main>
    <a href="https://github.com/DesktopMan/smx-api-docs?tab=readme-ov-file">SMX API Docs</a>
    <HeaderItem />
    <SimpleFilters />
    <UIFilters />
    <TableItem />
    <Skip />
  </main>
</template>

<style scoped>

</style>
