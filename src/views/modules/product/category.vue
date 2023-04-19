<template>
  <!-- <div>三级分类</div> -->
  <el-tree :data="menus" :props="defaultProps" @node-click="handleNodeClick">三级分类</el-tree>
</template>

<script>

export default {
    data() {
      return {
        menus: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        }
      };
    },
    methods: {
      handleNodeClick(data) {
        console.log(data);
      },
      getTreeList(){
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/product/category/list/tree'),
          method: 'get',
        }).then(({data}) => {
          console.log("成功加载的数据...",data.entityList);
          this.menus = data.entityList;
        })
      }
    },
    created(){
        this.getTreeList();
    }

}
</script>


<style>

</style>