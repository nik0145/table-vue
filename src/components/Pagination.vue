<template>
  <ul class="pagination">
    <li class="pagination-item">
      <button
        type="button"
        @click="onClickFirstPage"
        :disabled="isInFirstPage"
        aria-label="Go to first page"
        class="pag-btn pag-pad-l"
      >
        <i class="arrow left"></i><i class="arrow left" style="margin-left:-0.3rem"></i>
      </button>
    </li>

    <li class="pagination-item">
      <button
        type="button"
        @click="onClickPreviousPage"
        :disabled="isInFirstPage"
        aria-label="Go to previous page"
        class="pag-btn"
      >
        <i class="arrow left"></i>
      </button>
    </li>

    <li v-for="(page,i) in pages" class="pagination-item" :key="i" >
      <button
        type="button"
        @click="onClickPage(page.name)"
        :disabled="page.isDisabled"
        :class="{ active: isPageActive(page.name) }"
        class="pag-btn"
        :aria-label="`Go to page number ${page.name}`"
      >
        {{ page.name }}
      </button>
    </li>

    <li class="pagination-item">
      <button
        type="button"
        @click="onClickNextPage"
        :disabled="isInLastPage"
        aria-label="Go to next page"
        class="pag-btn pag-pad-r"
      >
        <i class="arrow right"></i>
      </button>
    </li>

    <li class="pagination-item">
      <button
        type="button"
        @click="onClickLastPage"
        :disabled="isInLastPage"
        aria-label="Go to last page"
        class="pag-btn pag-pad-r"
      >
        <i class="arrow right"></i><i class="arrow right" style="margin-left:-0.3rem"></i>
      </button>
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

<style scoped >
.pagination {
  display: flex;
  list-style: none;
}
  .pag-btn {
    width: 33px;
    height: 33px;
    font-weight: 500;
    color:#231f20;
    background: ghostwhite;
    border-radius: 50%;
    margin-right: 16px;
    border:2px solid dodgerblue;
    }
     .pag-btn:disabled {
      color: #b8b8b8;
      cursor: auto;
    }
      .arrow {
    border: solid grey;
    border-width: 0 1px 1px 0;
    display: inline-block;
    padding: 4px;
  }
   .active {
       background-color: #1774e2;
      color: #fff;
      border: 2px solid #1774e2;
      color: #231f20;
    }
    .right {
    transform: rotate(-45deg);
  }

  .left {
    transform: rotate(135deg);
  }
/* 
  .pag-btn {
    width: 4rem;
    height: 4rem;
    font-family: "Roboto", sans-serif;
    font-size: 1.6rem;
    font-weight: 500;
    color: $color-grey;
    background: $color-grey-light;
    border-radius: 50%;
    margin-right: 1rem;

    &:disabled {
      color: #b8b8b8;
      cursor: auto;
    }
    &.active {
      // background-color: $color-primary;
      // color: #fff;
      border: 2px solid $color-primary;
      color: $color-text;
    }

    // &:focus {
    //   border: 2px solid $color-primary;
    //   color: $color-text;
    // }
  }
  .arrow {
    border: solid $color-grey;
    border-width: 0 1px 1px 0;
    display: inline-block;
    padding: 4px;
  }

  .right {
    transform: rotate(-45deg);
  }

  .left {
    transform: rotate(135deg);
  } */
</style>
