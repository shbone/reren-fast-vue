<template>
  <!-- <div>三级分类</div> -->
  <el-tree
    :data="menus"
    :props="defaultProps"
    @node-click="handleNodeClick"
    :expand-on-click-node="false"
    show-checkbox
    node-key="data.catId"
  >
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button
          v-if="node.level <= 2"
          type="text"
          size="mini"
          @click="() => append(data)"
        >
          Append
        </el-button>
        <el-button
          v-if="node.childNodes.length == 0"
          type="text"
          size="mini"
          @click="() => remove(node, data)"
        >
          Delete
        </el-button>
      </span>
    </span>
  </el-tree>
</template>

<script>
export default {
  data() {
    return {
      menus: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  methods: {
    handleNodeClick(data) {
      // console.log(data);
    },
    append(data) {
      console.log("append", data);
      console.log("catID:", data.catId);
    },

    remove(node, data) {
      console.log("remove", node, data);
      var ids = [data.catId];
      this.$http({
        url: this.$http.adornUrl("/product/category/delete"),
        method: "post",
        data: this.$http.adornData(ids, false),
      }).then(({ data }) => {
        console.log("删除成功！");
        this.getTreeList;
      });
    },

    getTreeList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        method: "get",
      }).then(({ data }) => {
        console.log("成功加载的数据...", data.entityList);
        this.menus = data.entityList;
      });
    },
  },
  created() {
    this.getTreeList();
  },
};
</script>


<style>
</style>