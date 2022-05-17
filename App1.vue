<template>
  <div id="app">
    <div>第一步：把服务返回的数组复制到输入框中</div>
    <el-input type="textarea" v-model="text"></el-input>
    <div>第二步：生成数据并编辑表格</div>
    <div>
      <el-button @click="setData">生成数据</el-button>
      <!-- <el-button @click="setColumn">生成columns</el-button> -->
      <el-button @click="addColumn">新增一列</el-button>
      <el-button @click="editing=!editing">编辑</el-button>
      <el-button @click="saveColumns">下载</el-button>
    </div>
    <div style="margin-top:10px">
      <nested :editing="editing" :columns.sync="columns" />
    </div>
        <m-table   :columns="columns" :data='data' ></m-table>

  </div>
</template>

<script>
import mTable from "./mTable.vue";
import nested from "./nested";
import _ from "lodash";
let columnIndex = 0;
export default {
  name: "App",
  components: {
    mTable,
    nested,
  },
  methods: {
    saveColumns() {
      console.log(JSON.stringify(this.columns));
    },
    addColumn() {
      columnIndex++;
      this.columns.push({
        key: `column${columnIndex}`,
        label: `column${columnIndex}`,
      });
    },
    setData() {
      let arr = [];
      try {
        arr = JSON.parse(this.text);
      } catch (error) {
        console.log("无法解析");
      }
      this.data = arr;
      if (this.data.length) {
        this.setColumn();
      }
    },
    setColumn() {
      this.columns = Object.keys(this.data[0]).map((k) => ({
        key: k,
        label: k,
      }));
    },
  },
  watch: {},
  mounted() {
    // const arr = [];
    // for (let i = 0; i < 1; i++) {
    //   arr.push({
    //     date: "2016-05-03",
    //     name: "王小虎" + i,
    //     province: "上海",
    //     city: "普陀区",
    //     address: "上海市普陀区金沙江路 1518 弄",
    //     zip: i,
    //   });
    //   this.data = arr;
    //   this.text = JSON.stringify(arr);
    //   this.setColumn();
    // }
  },
  data() {
    return {
      text: `[{"date":"2016-05-03","name":"王小虎0","province":"上海","city":"普陀区","address":"上海市普陀区金沙江路 1518 弄","zip":0}]`,
      tableShow: true,
      columns: [],
      data: [],
      editing: false,
    };
  },
};
</script>

<style>
#app {
  padding: 20px;
}
</style>
