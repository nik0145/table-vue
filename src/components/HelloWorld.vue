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
        >
          найти
        </button>
         <button
          class="btn btn-outline-secondary"
          v-if="filter"
          @click="resetFilter"
          type="button"
        >
          Сбросить фильтр
        </button>
      </div>
    </div>
    <div class="alert alert-danger" v-if="isError" role="alert">
      Ошибка получения данных
    </div>
    <div v-if="isLoading" class="spinner-border text-primary" role="status">
      <span class="sr-only">Loading...</span>
    </div>
    <VTable :columns="columns" @selectRow="onSelectRow" :data="tableDataDisplay"/> 
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
import VTable from "./VTable.vue";

export default {
  name: "HelloWorld",
  components: {
    modal,
    Pagination,
    VTable,
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
      
      rowCurrent: null,
      isOpenModal: false,
      
      filter: "",
      columns: ["name", "company", "email", "address", "state"],
      tableData: [],
      tableDataDisplay: [],
      url: "https://app.dev-wazzup24.com/api/v1/wazzup_test/",
    };
  },
  props: {
    description: {
      type: String,
      default: "Загрузить пользователей",
    },
  },

  methods: {
    onSelectRow(row ){
      this.isOpenModal = true;
      this.rowCurrent = row;
    },
    resetFilter(){
      this.filter='';
      this.onChangeTableData();
    },
    onChangeTableData(){
      
        this.tableDataDisplay= this.tableData.slice(
        (this.pagination.currentPage - 1) * this.pagination.perPage,
        this.pagination.currentPage * this.pagination.perPage
      );
    },
    onPageChange(page) {
      this.pagination.currentPage = page;
      this.onChangeTableData();
    },
    closeModal() {
      this.isOpenModal = false;
    },
    filterBy: function() {
      let filter = this.filter.toLowerCase();
      this.tableDataDisplay = this.tableDataDisplay.filter((item) => {
        return Object.values(item).some(
          (prop) =>
            !filter || prop.toString().toLowerCase().includes(filter)
        );
      });
    },

    async onLoadData() {
      this.isLoading = true;
      const response = await fetch(this.url);
      if (response.ok) {
        let value = await response.json();
        this.tableData = value.map(item=>{
          const {add} = item
          delete item.add
          return {
            ...add,
            ...item
          }
        });
        this.pagination.totalPages = Math.ceil(
          this.tableData.length / this.pagination.perPage
        );
        this.pagination.totalItems = this.tableData.length;
        this.onChangeTableData()
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
