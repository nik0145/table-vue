<template>
  <ul class="pagination justify-content-end" style="margin-bottom:0px">
    <li class="page-item" :class="{ disabled: isInFirstPage }">
      <a
        class="page-link"
        @click.prevent="onClickFirstPage"
        href="#"
        aria-label="First"
      >
        <span aria-hidden="true">&laquo;</span>
        <span class="sr-only">Previous</span>
      </a>
    </li>
    <li class="page-item" :class="{ disabled: isInFirstPage }">
      <a
        class="page-link"
        @click.prevent="onClickFirstPage"
        href="#"
        tabindex="-1"
        >Previous</a
      >
    </li>

    <li
      v-for="(page, i) in pages"
      class="page-item"
      :class="{ active: isPageActive(page.name) }"
      :key="i"
    >
      <button
        type="button"
        @click.prevent="onClickPage(page.name)"
        :disabled="page.isDisabled"
        class="page-link"
        :aria-label="`Go to page number ${page.name}`"
      >
        {{ page.name }}
      </button>
    </li>
    <li class="page-item" :class="{ disabled: isInLastPage }">
      <a
        @click.prevent="onClickNextPage"
        class="page-link"
        href="#"
        >Next</a
      >
    </li>

    <li class="page-item" :class="{ disabled: isInLastPage }">
      <a
        @click.prevent="onClickLastPage"
        class="page-link"
        href="#"
        aria-label="Last"
      >
        <span aria-hidden="true">&raquo;</span>
      </a>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    totalPages: {
      type: Number,
      required: true,
    },
    total: {
      type: Number,
      required: true,
    },
    perPage: {
      type: Number,
      required: true,
    },
    currentPage: {
      type: Number,
      required: true,
    },
  },
  computed: {
    maxVisibleButtons() {
      return this.totalPages < 5 ? this.totalPages : 5;
    },
    startPage() {
      if (this.currentPage === 1) {
        return 1;
      }

      if (this.currentPage === this.totalPages) {
        return this.totalPages - this.maxVisibleButtons + 1;
      }

      return this.currentPage - 1;
    },
    endPage() {
      return Math.min(
        this.startPage + this.maxVisibleButtons - 1,
        this.totalPages
      );
    },
    pages() {
      const range = [];

      for (let i = this.startPage; i <= this.endPage; i += 1) {
        range.push({
          name: i,
          isDisabled: i === this.currentPage,
        });
      }

      return range;
    },
    isInFirstPage() {
      return this.currentPage === 1;
    },
    isInLastPage() {
      return this.currentPage === this.totalPages;
    },
  },
  methods: {
    onClickFirstPage() {
      this.$emit("pagechanged", 1);
    },
    onClickPreviousPage() {
      this.$emit("pagechanged", this.currentPage - 1);
    },
    onClickPage(page) {
      this.$emit("pagechanged", page);
    },
    onClickNextPage() {
      this.$emit("pagechanged", this.currentPage + 1);
    },
    onClickLastPage() {
      this.$emit("pagechanged", this.totalPages);
    },
    isPageActive(page) {
      return this.currentPage === page;
    },
  },
};
</script>

<style scoped></style>
