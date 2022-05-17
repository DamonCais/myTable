<template>
  <draggable
    class="dragArea"
    ghost-class="ghost"
    handle=".handle"
    tag="div"
    v-model="_columns"
    :group="{ name: 'g1' }"
  >
    <div class="handle" v-for="(el, i) in _columns" :key="i">
      <div>{{el.label}}</div>
      <nested-draggable :editing="editing" :columns.sync="el.children" />
    </div>
  </draggable>
</template>
<script>
import draggable from "vuedraggable";
export default {
  name: "nested-draggable",
  props: {
    editing: {
      type: Boolean,
      default: false,
    },
    columns: {
      type: Array | null,
    },
  },
  methods: {
    del(i) {
      this._columns.splice(i, 1);
    },
  },
  computed: {
    _columns: {
      set(val) {
        this.$emit("update:columns", val);
      },
      get() {
        return this.columns || [];
      },
    },
  },
  components: {
    draggable,
  },
};
</script>
<style lang="less" scoped>
.dragArea {
  min-height: 15px;
  padding-left: 10px;
  outline: 1px dashed;
  display: flex;
  flex-wrap: wrap;
}
.dragName {
  border: 1px solid #d4d4d4;
  border-radius: 3px;
  border-color: #d4d4d4;
  // padding: 6px;
  margin: 0;
  background: #f6f6f6;
}
.ghost {
  .dragName {
    border: 1px dotted #333;
    background: pink !important;
  }
}
.el-form-item {
  margin-bottom: 0 !important;
}
.flex {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.el-input {
  width: 100px;
}
.handle {
  margin-right: 10px;
  cursor: move;
}
.del {
  cursor: pointer;
}
</style>
