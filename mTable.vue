<script>
import {
  addResizeListener,
  removeResizeListener,
} from "element-ui/lib/utils/resize-event";
export default {
  props: ["columns", "tableData", "scrollHeight"],
  methods: {
    headerDragEnd(e) {
      this.setBodyHeightByLenght();
      this.$emit("headerDragEnd", e);
    },
    setPosition(e) {
      const { scrollTop, scrollLeft } = e.target;
      if (this.scrollLeft !== scrollLeft) {
        this.translateX(scrollLeft);
        this.scrollLeft = scrollLeft;
      }

      if (this.scrollTop === scrollTop) {
        return;
      }

      this.scrollTop = scrollTop;
      const start = Math.floor(scrollTop / 50);
      if (start >= 0) {
        this.rowStart = start;
        this.translateY(scrollTop);
      } else {
        this.rowStart = 0;
        this.translateY(0);
      }
    },
    setBodyHeightByLenght() {
      console.log("doLayout");
      const length = this.tableData.length;
      this.$refs.mTable.doLayout();
      if (this.$refs.mTable?.$refs?.bodyWrapper?.style) {
        this.$nextTick(() => {
          this.$refs.mTable.$refs.bodyWrapper.style.height = `${length * 50}px`;
          this.$refs.mTable.$refs.bodyWrapper.style.width =
            this.$refs.mTable.bodyWidth;
        });
      }
    },
    translateX(scrollLeft) {
      if (this.$refs.mTable?.$refs.headerWrapper) {
        this.$refs.mTable.$refs.headerWrapper.style.transform = `translateX(-${scrollLeft}px)`;
      }
    },
    translateY(scrollTop) {
      const start = Math.floor(scrollTop / 50);
      if (this.$refs.mTable?.$refs?.bodyWrapper?.firstChild) {
        this.$refs.mTable.$refs.bodyWrapper.firstChild.style.transform = `translateY(${
          start * 50
        }px)`;
      }

      if (this.$refs.mTable?.$refs?.fixedBodyWrapper?.firstChild.lastChild) {
        this.$refs.mTable.$refs.fixedBodyWrapper.firstChild.lastChild.style.transform = `translateY(-${
          scrollTop % 50
        }px)`;
      }
      if (
        this.$refs.mTable?.$refs?.rightFixedBodyWrapper?.firstChild.lastChild
      ) {
        this.$refs.mTable.$refs.rightFixedBodyWrapper.firstChild.lastChild.style.transform = `translateY(-${
          scrollTop % 50
        }px)`;
      }
    },
    renderColumn(h, colOptions) {
      console.log(colOptions);
      const that = this;
      const props = { props: colOptions };
      const { render, renderHeader } = colOptions;
      const prop = colOptions.key;
      const scopedSlots = {
        default(scope) {
          if (typeof render === "function") {
            return render.apply(that.$parent, [h, scope]);
          } else {
            return scope.row[colOptions.key];
          }
        },
      };
      if (typeof renderHeader === "function") {
        scopedSlots["header"] = function (scope) {
          return renderHeader(h, scope);
        };
      }

      colOptions.showTooltipWhenOverflow = true;
      if (colOptions?.children?.length) {
        <el-table-column prop={prop} column-key={prop} {...props}>
          {colOptions.children.map((v) => {
            this.renderColumn(h, v);
          })}
        </el-table-column>;
      }

      return (
        <el-table-column
          prop={prop}
          column-key={prop}
          label="abc"
          {...props}
          {...(["selection", "index"].includes(props["props"].type)
            ? {}
            : { scopedSlots })}
        ></el-table-column>
      );
    },
  },
  data() {
    return {
      rowStart: 0,
      scrollTop: null,
      scrollLeft: null,
    };
  },
  directives: {
    scroll: {
      bind: function (el, binding) {
        let height = binding.value;
        if (!height) {
          return;
        }
        const dom = el.querySelector(".el-table__body-wrapper");
        const div = document.createElement("div");
        div.classList = ["scroll-wrap"];
        div.id = "scroll-wrap";
        div.style.maxHeight = `${height}px`;
        div.style.overflowY = "auto";
        el.insertBefore(div, dom);
        div.appendChild(dom);
      },
    },
  },
  computed: {
    hasYScroll() {
      return Math.floor(this.scrollHeight / 50) < this.tableData.length;
    },
    showData() {
      return this.tableData.slice(this.rowStart, this.rowStart + 50);
    },
  },
  mounted() {
    window._t = this;
    this.$el
      .querySelector("#scroll-wrap")
      .addEventListener("scroll", this.setPosition);
    addResizeListener(this.$el, this.setBodyHeightByLenght);
  },
  beforeDestroy() {
    this.$el
      .querySelector("#scroll-wrap")
      .removeEventListener("scroll", this.setPosition);
    removeResizeListener(this.$el, this.setBodyHeightByLenght);
  },
  watch: {
    tableData() {
      this.setBodyHeightByLenght();
    },
  },
  render(h) {
    const scrollHeight = this.scrollHeight;
    return (
      <div
        class={this.hasYScroll ? "m-table-wrap m-table-wrap-y" : "m-table-wrap"}
      >
        <el-table
          ref="mTable"
          class="m-table"
          //   maxHeight={this.maxHeight}
          vScroll={scrollHeight}
          data={this.tableData}
          on-header-dragend={this.headerDragEnd}
          {...{ on: this.$listeners }}
          {...{ attrs: this.$attrs }}
        >
          {this.columns.map((v) => {
            return this.renderColumn(h, v);
          })}
          {this.$slots.empty ? this.$slots.empty : null}
          {this.$slots.append ? this.$slots.append : null}
        </el-table>
      </div>
    );
  },
};
</script>

<style lang="scss">
body ::-webkit-scrollbar {
  width: 11px;
  height: 11px;
}
.m-table-wrap {
  overflow: hidden;
  background: #f5f7fa;
}
.m-table-wrap-y {
  padding-right: 10px;
}
.m-table {
  overflow: visible !important;
  .el-table__body td {
    height: 50px;
  }
  .el-table__header-wrapper {
    overflow: visible;
  }
  .el-table__body-wrapper {
    overflow-y: hidden;
  }
  .scroll-wrap {
    overflow-y: auto;
    margin-right: -10px;
  }
  .scroll-wrap::-webkit-scrollbar {
    width: 10px;
    height: 10px;
  }
  .scroll-wrap::-webkit-scrollbar-thumb {
    background: #ddd;
    background-clip: padding-box;
    min-height: 28px;
    border-width: 8px;
  }
  .scroll-wrap:hover::-webkit-scrollbar-thumb {
    background: #ddd;
  }
  .scroll-wrap:hover::-webkit-scrollbar-thumb:hover {
    background: #1c1c1c;
    height: 9px;
    z-index: 1000;
  }
}
</style>
