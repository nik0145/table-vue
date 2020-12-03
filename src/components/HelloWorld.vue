<template>
  <div class="hello">
    
    <button
      type="button"
      @click="onLoadData"
      v-if="isEditing"
      class="btn btn-secondary"
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
    <!-- <div class="input-group mb-3">
  <input type="text" v-model="filter" @change="filterBy" class="form-control" placeholder="найти" aria-label="найти" aria-describedby="basic-addon1">
</div> -->
    <table v-if="tableData && tableData.length > 0" class="table table-hover">
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
        <tr @click="showModal(tableRow)" v-for="(tableRow, i) in tableData" :key="i">
          <td>{{ tableRow.name }}</td>
          <td>{{ tableRow.company }}</td>
          <td>{{ tableRow.email }}</td>
          <td>
            <span v-if="tableRow.add">{{ tableRow.add.state }}</span>
            <span v-else>Штат не указан</span>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- <button
      class="btn border-0 flex-grow-1 text-left shadow-none"
      :class="{ completed }"
      @click="$emit('on-toggle')"
      v-if="!isEditing"
    >
      <span>{{ description }}</span>
    </button> -->
    <modal :isOpen="isOpenModal" :value ="rowCurrent" @close="closeModal" />
  </div>
</template>

<script>
import modal from "./modal.vue";
export default {
  name: "HelloWorld",
  components: {
    modal,
  },
  data() {
    return {
      isEditing: true,
      sortReverse: false,
      rowCurrent:null,
      isOpenModal: false,
      sortColumn: "",
      filter: "",
      columns: ["address", "company", "email", "name", "state"],
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
  methods: {
    showModal(row) {
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
      let response = await fetch(this.url);

      if (response.ok) {
        let json = await response.json();
        this.tableData = json;
        console.log(this.tableData);
      } else {
        alert("Ошибка HTTP: " + response.status);
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
