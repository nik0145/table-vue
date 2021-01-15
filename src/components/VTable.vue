   <template>
   <div>
     <table
      v-if="data && data.length > 0"
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
          v-for="(tableRow, i) in data"
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
reverse:false,
sortReverse: false,
    };
  },
  methods:{
          byField: function(field) {
      return (a, b) =>
        a[field] > b[field] ? (this.reverse ? 1 : -1) : this.reverse ? -1 : 1;
    },
    sortBy: function(sortColumn) {
      this.reverse = this.sortColumn == sortColumn ? !this.reverse : false;
      this.sortColumn = sortColumn;
      this.tableData.sort(this.byField(sortColumn));
    },
  }
}
</script>

   
<style scoped>

</style>

  