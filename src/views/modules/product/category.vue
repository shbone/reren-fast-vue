<template>
  <div>
    <!-- <div>三级分类</div> -->
    <el-tree
      :data="menus"
      :props="defaultProps"
      @node-click="handleNodeClick"
      :expand-on-click-node="false"
      show-checkbox
      node-key="data.catId"
      :default-expanded-keys="['1']"
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

    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
      <el-form>
        <el-form-item label="活动名称" v-model="category">
          <el-input v-model="category.name"></el-input>
        </el-form-item>
        <!-- <el-form-item label="活动区域">
          <el-select placeholder="请选择活动区域"> </el-select>
        </el-form-item> -->
      </el-form>
      <!-- <span>这是一段信息</span> -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addCategory">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      menus: [],
      expandedkeys: [],
      dialogVisible: false,
      category: {
        name: "",
        catId: "",
        parentCid: "",
        catLevel: "",
        showStatus: "1",
        sort: "0",
      },
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },

  methods: {
    handleNodeClick(data) {
      console.log("click", data);
    },
    append(data) {
      console.log("append", data);
      console.log("catID:", data.catId);
      this.category.parentCid = data.catId;
      this.category.catLevel = data.catLevel + 1;
      this.dialogVisible = true;
    },
    open() {},
    remove(node, data) {
      console.log("remove", node, data);
      var ids = [data.catId];
      this.$confirm(`此操作将永久【${data.name}】, 是否继续?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/product/category/delete"),
            method: "post",
            data: this.$http.adornData(ids, false),
          }).then(({ data }) => {
            console.log("删除成功！");
            this.expandedkeys = [node.parent.data.catId];
            console.log(this.expandedkeys);
            this.getTreeList();
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: `已取消删除${data.name}`,
          });
        });
    },
    // 提交三级分类categoy
    addCategory() {
      console.log("提交的三级菜单信息", this.category);
      this.$http({
        url: this.$http.adornUrl("/product/category/save"),
        method: "post",
        data: this.$http.adornData(this.category, false),
      }).then(({ data }) => {
        this.$message({
          message: "菜单保存成功",
          type: "success",
        });
        // 刷新全部菜单
        this.getTreeList();
        // 展示菜单
        this.expandedkeys = [this.category.parentCid];

        console.log("parent:", this.category.parentCid);
        // 取消对话框
        this.dialogVisible = false;
      });
    },
    getTreeList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        method: "get",
      }).then(({ data }) => {
        // console.log("成功加载的数据...", data.entityList);
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