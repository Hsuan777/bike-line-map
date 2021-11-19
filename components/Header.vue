<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-primary">
    <div class="container">
      <h1 class="d-flex align-items-center">
        <NuxtLink to="/" class="navbar-brand text-white p-0 fz-larger">
          <img src="~/assets/icon/bike-line-logo.svg" alt="bike-line-log">
        </NuxtLink>
      </h1>
      <ul class="navbar-nav ms-auto">
        <li class="nav-item">
          <NuxtLink to="/" class="nav-link">
            尋找 U-bike
          </NuxtLink>
        </li>
        <li class="nav-item">
          <NuxtLink to="/recommend" class="nav-link">
            搜尋路線
          </NuxtLink>
        </li>
      </ul>
    </div>
  </nav>
</template>

<script>
export default {};
</script>
