<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.2/css/all.min.css"
    />

    <!-- <div class="wrapper">
      <nav>
        <RouterLink to="/">Home</RouterLink>|
        <RouterLink to="/about">About</RouterLink>
      </nav>
    </div> -->
  </header>

  <RouterView />
</template>

<style scoped></style>
