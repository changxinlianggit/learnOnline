<template>
  <div>
    <!--查询表单-->
    <el-form :model="params">
      <el-select v-model="params.siteId" placeholder="请选择站点">
        <el-option
          v-for="item in siteList"
          :key="item.siteId"
          :label="item.siteName"
          :value="item.siteId"
        ></el-option>
      </el-select>页面别名：
      <el-input v-model="params.pageAliase" style="width: 100px"></el-input>
      <el-button type="primary" v-on:click="query" size="small">查询</el-button>
      <router-link
        class="mui-tab-item"
        :to="{path:'/cms/page/add/',query:{
            page: this.params.page,
            siteId: this.params.siteId}}"
      >
        <el-button type="primary" size="small">新增页面</el-button>
      </router-link>
    </el-form>
    <el-table :data="list" stripe style="width: 100%">
      <el-table-column type="index" width="60"></el-table-column>
      <el-table-column prop="pageName" label="页面名称" width="280"></el-table-column>
      <el-table-column prop="pageAliase" label="别名" width="120"></el-table-column>
      <el-table-column prop="pageType" label="页面类型" width="100"></el-table-column>
      <el-table-column prop="pageWebPath" label="访问路径" width="230"></el-table-column>
      <el-table-column prop="pagePhysicalPath" label="物理路径" width="280"></el-table-column>
      <el-table-column label="操作" width="150">
        <template slot-scope="page">
          <el-button size="mini" type="warning" @click="edit(page.row.pageId)">编辑</el-button>
          <el-button size="mini" type="danger" @click="deleteByid(page.row.pageId)">删除</el-button><br>
          <el-button @click="preview(page.row.pageId)" type="mini" size="primary">预览</el-button>
          <el-button @click="postpage(page.row.pageId)" type="mini" size="success">发布</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      layout="prev, pager, next"
      :total="total"
      :page-size="params.size"
      :current-page="params.page"
      v-on:current-change="changePage"
      style="float:right"
    ></el-pagination>
  </div>
</template>
<script>
/*编写页面静态部分，即model及vm部分。*/
import * as cmsApi from "../api/cms";
export default {
  data() {
    return {
      siteList: [],
      list: [],
      total: 0,
      params: {
        page: 1,
        size: 5,
        siteId: "",
        papageAliase: ""
      }
    };
  },
  created: function() {
    //从路由上获取参数
    this.params.page = Number.parseInt(this.$route.query.page || 1);
    this.params.siteId = this.$route.query.siteId || "";
    //初始化站点列表
    this.siteList = [
      {
        siteId: "5a751fab6abb5044e0d19ea1",
        siteName: "门户主站"
      },
      {
        siteId: "102",
        siteName: "测试站"
      }
    ];
  },
  methods: {
    //模糊查询
    query: function() {
      // alert('查询')
      //调用服务端的接口
      cmsApi
        .page_list(this.params.page, this.params.size, this.params)
        .then(res => {
          //将res结果数据赋值给数据模型对象
          this.list = res.queryResult.list;
          this.total = res.queryResult.total;
        });
    },
    //获取下一页的数据
    changePage: function(page) {
      //形参就是当前页码
      //调用query方法
      // alert(page)
      this.params.page = page;
      this.query();
    },
    //单击修改按钮调用,修改页面的数据
    edit: function(pageId) {
      alert(pageId);
      this.$router.push({
        path: "/cms/page/edit/" + pageId,
        query: {
          page: this.params.page,
          siteId: this.params.siteId
        }
      });
    },
    deleteByid: function(pageId){
      this.$confirm("确认删除吗？", "提示", {}).then(res => {
        cmsApi.deleteByid(pageId).then(res =>{
          if(res.success){
            this.$message.success("删除成功");
            this.query();
          }else{
            this.$message.console.error("删除成功");
          }
        });
      });
    },
    //页面预览
    preview: function (row){
      window.open("http://www.xuecheng.com/cms/preview/"+row)
    },
    //页面发布
    postpage: function (row){
      this.$confirm("确认发布吗？", "提示", {}).then(res => {
        cmsApi.postpage(row).then(spon=>{
          if(spon.success){
            this.$message.success("发布成功,请稍后查询检查结果");
          }else{
            this.$message.error("发布失败");
          }
        })
      })
    }
  },
  //钩子函数，当页面渲染完成后调用
  mounted() {
    //当DOM元素渲染完成后调用query
    this.query();
    //初始化站点列表
    this.siteList = [
      {
        siteId: "5a751fab6abb5044e0d19ea1",
        siteName: "门户主站"
      },
      {
        siteId: "102",
        siteName: "测试站"
      }
    ];
  }
};
</script>
<style>
/*编写页面样式，不是必须*/
</style>
