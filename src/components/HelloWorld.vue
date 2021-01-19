<template>
  <div class="hello">
    <nav class="navbar navbar-dark fixed-top bg-dark flex-md-nowrap p-0 shadow">
      <a
        class="navbar-brand col-sm-3 col-md-2 mr-0"
        target="_blank"
        href="https://github.com/nik0145"
        >nik0145</a
      >
      <div class="input-group form-control-dark">
        <input
          class="form-control form-control-dark  py-2 border-right-0"
          type="search"
          v-model="filter"
          placeholder="Поиск по таблице"
          aria-label="Поиск по таблице"
        />
        <span class="input-group-append">
          <button
            v-if="filter"
            @click="resetFilter"
            class="btn btn-outline-secondary"
            type="button"
          >
            <img class="icon" src="@/assets/close.svg" />
          </button>
        </span>
      </div>

      <ul class="navbar-nav px-3">
        <li class="nav-item text-nowrap">
          <a class="nav-link" @click="filterBy" href="#">Search</a>
        </li>
      </ul>
    </nav>

    <div class="container-fluid">
      <div class="row">
        <nav class="col-md-2 d-none d-md-block bg-light sidebar">
          <div class="sidebar-sticky">
            <ul class="nav flex-column">
              <li class="nav-item">
                <a class="nav-link active" href="#">
                  <span data-feather="home"></span>
                  Users <span class="sr-only">(current)</span>
                </a>
              </li>
            </ul>
          </div>
        </nav>

        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 px-4">
          <div
            class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom"
          >
            <h1 class="h2">Users</h1>
            <div class="btn-toolbar mb-2 mb-md-0">
              <button
                class="btn btn-sm btn-outline-secondary"
                @click="onLoadData"
                v-if="isEditing"
                type="button"
              >
                {{ description }}
              </button>
            </div>
          </div>

          <div class="alert alert-danger" v-if="isError" role="alert">
            Ошибка получения данных
          </div>
          <div
            v-if="isLoading"
            class="spinner-border text-primary"
            role="status"
          >
            <span class="sr-only">Loading...</span>
          </div>
          <div class="table-responsive">
            <VTable
              :columns="columns"
              @selectRow="onSelectRow"
              :data="tableDataDisplay"
            />
          </div>
          <Pagination
            v-if="pagination"
            :total-pages="pagination.totalPages"
            :total="pagination.totalItems"
            :per-page="pagination.perPage"
            :current-page="pagination.currentPage"
            @pagechanged="onPageChange"
          />

          <modal
            :isOpen="isOpenModal"
            :value="rowCurrent"
            @close="closeModal"
          />
        </main>
      </div>
    </div>
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
        perPage: 5,
        totalPages: 0,
        totalItems: 0,
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
    onSelectRow(row) {
      this.isOpenModal = true;
      this.rowCurrent = row;
    },
    resetFilter() {
      this.filter = "";
      this.onChangeTableData();
    },
    onChangeTableData() {
      this.tableDataDisplay = this.tableData.slice(
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
            !filter ||
            prop
              .toString()
              .toLowerCase()
              .includes(filter)
        );
      });
    },

    async onLoadData() {
      this.isLoading = true;
      const response = await fetch(this.url);
      if (response.ok) {
        let value = await response.json();
        this.tableData = value.map((item) => {
          const { add } = item;
          delete item.add;
          return {
            ...add,
            ...item,
          };
        });
        this.pagination.totalPages = Math.ceil(
          this.tableData.length / this.pagination.perPage
        );
        this.pagination.totalItems = this.tableData.length;
        this.onChangeTableData();
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
.icon {
  width: 10px;
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
body {
  font-size: 0.875rem;
}

.feather {
  width: 16px;
  height: 16px;
  vertical-align: text-bottom;
}

/*
 * Sidebar
 */

.sidebar {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  z-index: 100; /* Behind the navbar */
  padding: 48px 0 0; /* Height of navbar */
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, 0.1);
}

@media (max-width: 767.98px) {
  .sidebar {
    top: 5rem;
  }
}

.sidebar-sticky {
  position: relative;
  top: 0;
  height: calc(100vh - 48px);
  padding-top: 0.5rem;
  overflow-x: hidden;
  overflow-y: auto; /* Scrollable contents if viewport is shorter than content. */
}

@supports ((position: -webkit-sticky) or (position: sticky)) {
  .sidebar-sticky {
    position: -webkit-sticky;
    position: sticky;
  }
}

.sidebar .nav-link {
  font-weight: 500;
  color: #333;
}

.sidebar .nav-link .feather {
  margin-right: 4px;
  color: #999;
}

.sidebar .nav-link.active {
  color: #007bff;
}

.sidebar .nav-link:hover .feather,
.sidebar .nav-link.active .feather {
  color: inherit;
}

.sidebar-heading {
  font-size: 0.75rem;
  text-transform: uppercase;
}

/*
 * Navbar
 */

.navbar-brand {
  padding-top: 0.75rem;
  padding-bottom: 0.75rem;
  font-size: 1rem;
  background-color: rgba(0, 0, 0, 0.25);
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, 0.25);
}

.navbar .navbar-toggler {
  top: 0.25rem;
  right: 1rem;
}

.navbar .form-control {
  padding: 0.75rem 1rem;
  border-width: 0;
  border-radius: 0;
}

.form-control-dark {
  color: #fff;
  background-color: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 255, 255, 0.1);
}

.form-control-dark:focus {
  border-color: transparent;
  box-shadow: 0 0 0 3px rgba(255, 255, 255, 0.25);
}
</style>
