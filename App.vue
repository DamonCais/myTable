<template>
  <div style="padding: 20px">
    <div>
      <el-button @click="test">test</el-button>
    </div>
    <m-table
      :tableData="tableData"
      border
      :scrollHeight="500"
      :columns="columns"
    ></m-table>
  </div>
</template>

<script>
import mTable from "./mTable";
export default {
  components: {
    mTable,
  },
  methods: {
    test() {
      this.tableData = this.tableData.slice(0, 5);
    },
  },
  mounted() {
    const arr = [];
    for (let i = 0; i < 100; i++) {
      arr.push({
        date: "2016-05-02" + i,
        name: "王小虎" + i,
        province: "上海",
        city: "普陀区",
        address: "上海市普陀区金沙江路 1518 弄" + i,
        zip: 200333 + i,
      });
    }
    this.tableData = arr;
  },
  data() {
    return {
      tableData: [],
      columns: [
        {
          fixed: "left",
          key: "date",
          label: "date",
        },
        {
          key: "date",
          label: "date",
          width: 500,
        },
        {
          key: "date",
          label: "date",
          width: 500,
        },
        {
          key: "date",
          label: "date",
          width: 500,
        },
        {
          key: "date",
          label: "date",
          width: 500,
        },
        {
          key: "date",
          label: "date",
        },
        {
          fixed: "right",
          key: "name",
          label: "name",
        },
      ],
    };
  },
};
</script>

<style></style>
