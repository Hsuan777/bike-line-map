<template>
  <nav v-if="pages > 1" aria-label="Page navigation">
    <ul class="pagination mb-0">
      <li class="page-item me-lg-3" :class="{ disabled: currentPage === 1 }">
        <a class="page-link" href="#" @click.prevent="clickPage(currentPage - 1)">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-chevron-left"
            viewBox="0 0 16 16"
          >
            <path
              fill-rule="evenodd"
              d="M11.354 1.646a.5.5 0 0 1 0 .708L5.707 8l5.647
              5.646a.5.5 0 0 1-.708.708l-6-6a.5.5 0 0 1 0-.708l6-6a.5.5 0 0 1 .708 0z"
            />
          </svg>
        </a>
      </li>
      <li
        v-for="item in pages"
        :key="item"
        class="page-item me-3 d-none d-lg-block"
        :class="{ active: currentPage === item }"
      >
        <a
          class="page-link d-flex flex-column rounded"
          :class="{ 'px-4': item < 10 }"
          href="#"
          @click.prevent="clickPage(item)"
        >
          {{ item }}
        </a>
      </li>
      <li class="page-item mx-5 d-lg-none">
        <p class="d-flex align-items-center h-100">
          第 {{ currentPage }} 頁
        </p>
      </li>
      <li class="page-item" :class="{ disabled: currentPage === pages }">
        <a class="page-link" href="#" @click.prevent="clickPage(currentPage + 1)">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-chevron-right"
            viewBox="0 0 16 16"
          >
            <path
              fill-rule="evenodd"
              d="M4.646 1.646a.5.5 0 0 1 .708 0l6 6a.5.5 0 0 1 0
                .708l-6 6a.5.5 0 0 1-.708-.708L10.293 8 4.646 2.354a.5.5 0 0 1 0-.708z"
            />
          </svg>
        </a>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  props: {
    pages: {
      type: Number,
      default: null,
    },
    currentPage: {
      type: Number,
      default: null,
    },
  },
  emits: ['display-page'],
  methods: {
    clickPage(num) {
      window.scrollTo(0, 0);
      this.$emit('display-page', num);
    },
  },
};
</script>
