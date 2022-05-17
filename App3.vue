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
      <m-table @loadMore="loadMore" class="my-table" ref="mTable" scrollHeight="500"  :columns="columns" :tableData='data' >
      </m-table>
  </div>
</template>

<script>
import mTable from "./myTable.vue";
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
    setAppend() {
      // 1.option
      const options = {
        root: this.$refs.mTable.$el,
        rootMargin: "0px",
        threshold: 0.2,
      };

      // 2.function
      function handleChange(entries) {
        console.log("↓--------------↓");
        console.log("element innerHTML->", entries[0].target.innerHTML);
        console.log("isIntersecting->", entries[0].isIntersecting);
        // console.log("entries->", entries);
      }

      // 3.element
      const obInner3 = this.$refs.append.$el;
      const intersectionObserver = new IntersectionObserver(
        handleChange,
        options
      );
      intersectionObserver.observe(obInner3);
    },
    loadMore() {
      console.log("loadMore");
      setTimeout(() => {
        const arr = [];
        for (let i = 0; i < 30; i++) {
          arr.push({
            date: "2016-05-03",
            name: "王小虎" + i,
            province: "上海",
            city: "普陀区",
            address: "上海市普陀区金沙江路 1518 弄",
            zip: i,
          });
        }
        this.data = this.data.concat(arr);
        this.$refs.mTable.finishLoadMore();
      }, 5000);
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
    window._t = this;
    this.setData();
    const arr = [];
    for (let i = 0; i < 30; i++) {
      arr.push({
        date: "2016-05-03",
        name: "王小虎" + i,
        province: "上海",
        city: "普陀区",
        address: "上海市普陀区金沙江路 1518 弄",
        zip: i,
      });
      this.data = arr;
      // this.setAppend();
      // this.$nextTick(() => {
      //   this.setAppend();
      // });
      // this.text = JSON.stringify(arr);
      // this.setColumn();
    }
  },
  data() {
    return {
      text: `[{"date":"2016-05-03","name":"王小虎0","province":"上海","city":"普陀区","address":"上海市普陀区金沙江路 1518 弄","zip":0}]`,
      tableShow: true,
      columns: [],
      data: [],
      data2: [],
      editing: false,
    };
  },
};
</script>

<style>
#app {
  padding: 20px;
}
.append {
  line-height: 50px;
  text-align: center;
}
</style>
