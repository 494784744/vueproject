<template>
  <el-card class="question-container" shadow="never">
    <div slot="header">
      <el-row>
        <el-button type="primary" size="mini" @click="skipNewText">新增试题</el-button>
        <el-button type="primary" size="mini">批量导入</el-button>
      </el-row>
    </div>
    <!-- 表单 -->
    <el-form :inline="true">
      <!-- 第一行 -->
      <el-row type="flex" justify="start">
        <el-form-item label="学科">
          <el-select v-model="serchFrom.subjectID" placeholder="请选择">
            <el-option
              v-for="item in subjectID"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="难度">
          <el-select v-model="serchFrom.difficulty" placeholder="请选择">
            <el-option
              v-for="item in difficulty"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="试题类型">
          <el-select v-model="serchFrom.questionType" placeholder="请选择">
            <el-option
              v-for="item in questionType"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签">
          <el-select v-model="serchFrom.tags" placeholder="请选择">
            <el-option
              v-for="item in tagslist"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-row>
      <!-- 第二行 -->
      <el-row type="flex" justify="center">
        <el-form-item label="省份">
          <el-select v-model="serchFrom.province" placeholder="请选择" @change="getCitys">
            <el-option v-for="(item,index) in provinces" :key="index" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市">
          <el-select v-model="serchFrom.city" placeholder="请选择">
            <el-option v-for="(item,index) in citys" :key="index" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="关键字">
          <el-input v-model="serchFrom.keyword" placeholder="请输入内容"></el-input>
        </el-form-item>
        <el-form-item label="题目备注">
          <el-input v-model="serchFrom.remarks" placeholder="请输入内容"></el-input>
        </el-form-item>
      </el-row>
      <!-- 第三行 -->
      <el-row type="flex" justify="end">
        <el-form-item label="企业简称">
          <el-input v-model="serchFrom.shortName" placeholder="请输入内容"></el-input>
        </el-form-item>
        <el-form-item label="方向">
          <el-select v-model="serchFrom.direction" placeholder="请选择">
            <el-option v-for="(item,index) in direction" :key="index" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="录入人">
          <el-select v-model="serchFrom.creatorID" placeholder="请选择">
            <el-option
              v-for="item in directoryspeaplo"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级目录">
          <el-input v-model="serchFrom.catalogID" placeholder="请输入内容"></el-input>
        </el-form-item>
      </el-row>
      <!-- 按钮 -->
      <div style="height:60px">
        <el-row type="flex" justify="center">
          <el-button size="mini">清除</el-button>
          <el-button type="primary" size="mini">搜索</el-button>
        </el-row>
      </div>
    </el-form>
    <!-- 列表 -->
    <el-table ref="singleTable" highlight-current-row :data="tableData" style="width: 100%">
      <el-table-column property="id" label="序号" type="index" width="100"></el-table-column>
      <el-table-column property="number" label="试题编号" width="120"></el-table-column>
      <el-table-column :formatter="fmattersubjectID" property="subjectID" label="学科" width="120"></el-table-column>
      <el-table-column
        :formatter="formatterquestionType"
        prop="questionType"
        label="题型"
        width="120"
      ></el-table-column>
      <el-table-column property="question" label="题干" width="120"></el-table-column>
      <el-table-column property="addDate" label="录入时间" width="240"></el-table-column>
      <el-table-column
        :formatter="formatterdifficulty"
        property="difficulty"
        label="难度"
        width="120"
      ></el-table-column>
      <el-table-column property="creator" label="录入人" width="120"></el-table-column>
      <el-table-column property="operation" label="操作" width>
        <el-button type="text">预览</el-button>
        <el-button type="text">修改</el-button>
        <el-button type="text">删除</el-button>
        <el-button type="text">加入精选</el-button>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-row style="height:60px;" type="flex" justify="end" align="middle">
      <el-pagination
        background
        layout="prev, pager, next"
        :total="this.page.total"
        :page-sizes="page.pageSize"
        :current-page="page.currentPage"
        @current-change="cheagePage"
      ></el-pagination>
    </el-row>
  </el-card>
</template>

<script>
import { list, detail } from "../../api/hmmm/questions";
import { simple as subjectlist } from "../../api/hmmm/subjects";
import { simple as tagslist } from "../../api/hmmm/tags";
import { direction, questionType, difficulty } from "../../api/hmmm/constants";
import { provinces, citys } from "../../api/hmmm/citys";
export default {
  data() {
    return {
      // 第一行
      subjectID: [], // 学科列表
      difficulty, // 试题难度
      questionType, // 试题类型
      tagslist: [], // 标签列表
      // 第二行
      provinces: [], // 城市列表
      citys: [], // 区域列表
      //第三行
      direction, // 方向列表
      directoryspeaplo: [
        {
          value: "1",
          label: "超级管理员"
        },
        {
          value: "2",
          label: "编辑"
        }
      ], // //录入人列表

      serchFrom: {
        // 第一行
        subjectID: "", // 学科
        difficulty: "", // 试题难度
        questionType: "", // 试题类型
        tags: "" ,// 标签
        // 第二行
        province:"" ,// 所在省份
        city:"", // 所在城市
        keyword:"" ,// 关键字
        remarks:"", // 题目备注
        // 第三行
        shortName:"", //企业简称
        direction:"", //方向
        creatorID:"", //录入人
        catalogID:"" //目录
      }, // 搜索栏封装
      
      tableData: [
        // {
        //   id: "1",
        //   number: "2",
        //   subjectID: "3",
        //   questionType: "4",
        //   question: "5",
        //   addDate: "6",
        //   difficulty: "7",
        //   creator:'8',
        //   operation: "9"
        // }
      ], // 基础列表
      page: {
        total: 0, //总页数
        currentPage: 1, // 默认当前页数，第一条页数
        pageSize: [10] // 默认每页条数,好像默认就是10
      },
      currentRow: null
    };
  },
  methods: {
    // 点击跳转到新增页面
    skipNewText() {
      this.$router.push("/questions/new");
    },
    // 获取基础试题列表
    async getbasequest() {
      let result = await list({
        page: this.page.currentPage, // 默认请求第1条数据
        pagesize: this.page.pageSize[0] // 请求每页多少条
      });
      // console.log(result.data);
      this.tableData = result.data.items;
      this.page.total = result.data.counts;
    },
    // 改变页数切换页面
    cheagePage(newpage) {
      this.page.currentPage = newpage;
      this.getbasequest();
    },
    // 转换格式
    // 转换题型格式
    formatterquestionType(row, column, cellValue, index) {
      // row 当前行数据
      // column 当前列数据
      // cellValue 当前单元格的值
      // index 当前下标
      switch (row.questionType) {
        case "1":
          return "单选";
        case "2":
          return "多选";
        case "3":
          return "简单";
        default:
          return cellValue;
      }
    },
    // 转换难度格式
    formatterdifficulty(row, column, cellValue, index) {
      switch (row.difficulty) {
        case "1":
          return "简单";
        case "2":
          return "一般";
        case "3":
          return "困难";
        default:
          return row.difficulty;
      }
    },
    //转换学科格式
    fmattersubjectID(row, column, cellValue, index) {
      switch (row.subjectID) {
        case 16:
          return "大数据";
        case 14:
          return "python";
        case 13:
          return "c";
        case "3":
          return "困难";
        case "3":
          return "困难";
        case "3":
          return "困难";
        case "1":
          return "简单";
        case "2":
          return "一般";
        case "3":
          return "困难";
        case "3":
          return "困难";
        case "3":
          return "困难";
        case 1:
          return "java";
        default:
          return row.subjectID;
      }
      //0: {value: 16, label: "大数据"}
      // 1: {value: 14, label: "python"}
      // 2: {value: 13, label: "c"}
      // 3: {value: 12, label: "c#"}
      // 4: {value: 11, label: "php"}
      // 5: {value: 10, label: "运维"}
      // 6: {value: 9, label: "算法"}
      // 7: {value: 8, label: "数据库"}
      // 8: {value: 7, label: "c++"}
      // 9: {value: 6, label: "产品"}
      // 10: {value: 5, label: "设计"}
      // 11: {value: 4, label: "前端"}
      // 12: {value: 3, label: "安卓"}
      // 13: {value: 2, label: "ios"}
      // 14: {value: 1, label: "java"}
    },
    // 获取学科列表
    async getSubjectList() {
      let result = await subjectlist();
      this.subjectID = result.data;
    },
    // 获取标签列表
    async getTagsList() {
      let result = await tagslist();
      this.tagslist = result.data;
    },
    // 获取城市列表
    async getProvinces() {
      this.provinces = await provinces();
    }
  },
  computed: {
    // 根据城市列表获取区域列表
    getCitys() {
      return (this.citys = citys(this.serchFrom.province));
    }
  },
  created() {
    this.getbasequest(); // 获取基础试题列表
    this.getSubjectList(); // 获取学科列表
    this.getTagsList(); // 获取标签列表
    this.getProvinces(); // 获取城市列表
  }
};
</script>

<style lang="scss" scoped>
.question-container {
  min-height: 100vh;
}
</style>
