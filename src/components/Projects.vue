<template>
  <section id="projects" class="py-5">
    <div class="container">
      <h2 class="text-center mb-5">My Projects</h2>

      <div class="row justify-content-center g-4">
        <div
          v-for="project in projects"
          :key="project.id"
          class="col-12 col-sm-6 col-md-4 d-flex"
        >
          <ProjectCard :project="project" />
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import ProjectCard from './ProjectCard.vue'
import projects from '../data/projects.json'
</script>
