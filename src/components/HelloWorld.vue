<template>
  <div class="hello">
   
    <button
      type="button"
      @click="onLoadData"
      v-if="isEditing"
      class="btn btn-secondary mb-3"
    >
      {{ description }}
    </button>
    <div class="input-group mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="фильтр"
        aria-label="фильтр"
        v-model="filter"
        aria-describedby="button-addon2"
      />
      <div class="input-group-append">
        <button
          class="btn btn-outline-secondary"
          @click="filterBy"
          type="button"
          id="button-addon2"
        >
          найти
        </button>
      </div>
    </div>
    <div class="alert alert-danger" v-if="isError" role="alert">
      Ошибка получения данных
    </div>
    <div v-if="isLoading" class="spinner-border text-primary" role="status">
      <span class="sr-only">Loading...</span>
    </div>

    <table
      v-if="tableDataDisplay && tableDataDisplay.length > 0"
      class="table table-hover"
    >
      <thead>
        <tr>
          <th
            v-for="(tableTh, i) in columns"
            scope="col"
            class="cursor-pointer"
            :key="i"
            @click="sortBy(tableTh)"
          >
            {{ tableTh }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          @click="showModal(tableRow)"
          v-for="(tableRow, i) in tableDataDisplay"
          :key="i"
        >
          <td>{{ tableRow.name }}</td>
          <td>{{ tableRow.company }}</td>
          <td>{{ tableRow.email }}</td>
          <td>{{ tableRow.address }}</td>
          <td>
            <span v-if="tableRow.add">{{ tableRow.add.state }}</span>
            <span v-else>Штат не указан</span>
          </td>
        </tr>
      </tbody>
    </table>
     <Pagination
      v-if="pagination"
      :total-pages="pagination.totalPages"
      :total="pagination.totalItems"
      :per-page="pagination.perPage"
      :current-page="pagination.currentPage"
      @pagechanged="onPageChange"
    />

    <modal :isOpen="isOpenModal" :value="rowCurrent" @close="closeModal" />
  </div>
</template>

<script>
import modal from "./modal.vue";
import Pagination from "./Pagination.vue";

export default {
  name: "HelloWorld",
  components: {
    modal,
    Pagination,
  },
  data() {
    return {
      isEditing: true,
      isInFirstPage: false,
      pagination: {
        currentPage: 1,
        perPage: 10,
        totalPages: 10,
        totalItems: 10,
      },
      isError: false,
      isLoading: false,
      sortReverse: false,
      rowCurrent: null,
      isOpenModal: false,
      sortColumn: "",
      filter: "",
      columns: ["name", "company", "email", "address", "state"],
      tableData: [],
      url: "https://app.dev-wazzup24.com/api/v1/wazzup_test/",
    };
  },
  props: {
    description: {
      type: String,
      default: "Загрузить пользователей",
    },
  },
  computed: {
    tableDataDisplay() {
      return this.tableData.slice(
        (this.pagination.currentPage - 1) * this.pagination.perPage,
        this.pagination.currentPage * this.pagination.perPage
      );
    },
  },

  methods: {
    onPageChange(page) {
      this.pagination.currentPage = page;
    },
    showModal(row) {
      console.log(this.kek);
      this.isOpenModal = true;
      this.rowCurrent = row;
    },
    closeModal() {
      this.isOpenModal = false;
    },
    filterBy: function() {
      //! не забыть допилить стейт
      let filter = this.filter.toLowerCase();
      console.log(this.filter);
      let kek = this.tableData.filter((item) => {
        return Object.values(item).some(
          (prop) =>
            !filter ||
            (typeof prop === "string"
              ? prop.includes(filter)
              : prop.toString().includes(filter)) // !нужно допилить для state
        );
      });
      this.tableData = kek;
    },
    byField: function(field) {
      return (a, b) =>
        a[field] > b[field] ? (this.reverse ? 1 : -1) : this.reverse ? -1 : 1;
    },
    sortBy: function(sortColumn) {
      this.reverse = this.sortColumn == sortColumn ? !this.reverse : false;
      this.sortColumn = sortColumn;
      this.tableData.sort(this.byField(sortColumn));
    },
    async onLoadData() {
      this.isLoading = true;
      const response = await fetch(this.url);
      if (response.ok) {
        let value = await response.json();
        this.tableData = [...value];
        this.pagination.totalPages = Math.ceil(
          this.tableData.length / this.pagination.perPage
        );
        this.pagination.totalItems = this.tableData.length;
        this.isLoading = false;
      } else {
        this.isError = true;
        this.isLoading = false;
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.cursor-pointer {
  cursor: pointer;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
