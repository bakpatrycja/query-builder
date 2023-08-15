<script setup>
import { ref, computed } from 'vue'
import CrudQueryBuilder from '../pages/CrudQueryBuilder.vue'
import ProjectInfo from '../pages/ProjectInfo.vue'
import NotFound from '../pages/NotFound.vue'

const routes = {
  '/': ProjectInfo,
  '/create': CrudQueryBuilder,
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>

<template>

  <a href="#/">Project Info</a> | 
  <a href="#/create">CRUD Query Builder</a> | 
  <a href="#/non-existent-path">Router test: page doesn't exist </a>
  <component :is="currentView" />
  <br>
  <br>
</template>
