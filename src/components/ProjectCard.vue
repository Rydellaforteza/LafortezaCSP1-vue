<template>
  <div class="card h-100 shadow-sm rounded flex-fill">
    <img :src="project.image" class="card-img-top img-fluid" alt="project image" />
    <div class="card-body text-center">
      <h5 class="card-title">{{ project.title }}</h5>
      <p class="card-text">{{ project.description }}</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  project: Object
})
</script>