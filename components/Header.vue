<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-primary">
    <div class="container">
      <h1 class="d-flex align-items-center">
        <NuxtLink to="/" class="navbar-brand text-white p-0 fz-larger">
          <img src="~/assets/icon/bike-line-logo.svg" alt="bike-line-log">
        </NuxtLink>
      </h1>
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon" />
      </button>
      <div id="navbarSupportedContent" class="collapse navbar-collapse">
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
    </div>
  </nav>
</template>

<script>
export default {};
</script>
