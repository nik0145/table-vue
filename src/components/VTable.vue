<template>
  <div>
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
            <img
              v-if="sortReverse === 1 && sortColumn === tableTh"
              class="icon"
              src="@/assets/up.svg"
            />
            <img
              v-if="sortReverse === -1 && sortColumn === tableTh"
              class="icon"
              src="@/assets/down.svg"
            />
            <img
              v-if="sortColumn !== tableTh || sortReverse === 0"
              class="icon"
              src="@/assets/sort-arrows.svg"
            />
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          @click="onSelectRow(tableRow)"
          v-for="(tableRow, i) in tableData"
          :key="i"
        >
          <td>{{ tableRow.name }}</td>
          <td>{{ tableRow.company }}</td>
          <td>{{ tableRow.email }}</td>
          <td>{{ tableRow.address }}</td>
          <td> {{ tableRow.state }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "Table",
  props: {
    data: {
      type: Array,
      required: true,
    },
    columns: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      sortColumn: "",
      reverse: false,
      tableData: [],
      sortReverse: 0,
    };
  },
  watch: {
    data(v) {
      this.tableData = [...v];
    },
  },
  methods: {
    onSelectRow(row) {
      this.$emit("selectRow", row);
    },
    byField: function(field) {
      return (a, b) =>
        a[field] > b[field]
          ? this.sortReverse > 0
            ? 1
            : -1
          : this.sortReverse > 0
          ? -1
          : 1;
    },
    onChangeReverse(reverse) {
      switch (reverse) {
        case 1:
          return 0;
        case 0:
          return -1;
        case -1:
          return 1;
        default:
          return 0;
      }
    },
    sortBy: function(sortColumn) {
      console.log("sortColumn", sortColumn);
      console.log("sortReverse", this.sortReverse);
      // * -1 сортировка desc
      // * 1 сортировка asc
      // * 0 default
      this.sortReverse =
        this.sortColumn === sortColumn
          ? this.onChangeReverse(this.sortReverse)
          : -1;
      this.sortColumn = sortColumn;
      if (this.sortReverse === 0) {
        this.tableData = [...this.data];
      } else {
        this.tableData.sort(this.byField(sortColumn));
      }
    },
  },
};
</script>

<style scoped>
.icon {
  width: 10px;
}
</style>
