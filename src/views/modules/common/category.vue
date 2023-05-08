<template>
    <el-tree
      :data="menus"
      :props="defaultProps"
      node-key="data.catId"
      ref="menuTree"
      @node-click="nodeclick"
    ></el-tree>
</template>

<script>
export default {
  data() {
    return {
      menus: [],
      expandedkeys: [],
      dialogVisible: false,
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  methods:{
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
    nodeclick(data,node,component){
      console.log("子组件被点击：",data,node,component);
        //给父节点发送事件
        this.$emit("tree-node-click",data,node,component)
    }
  },
  created(){
    this.getTreeList();
  }
};
</script>

<style>
</style>