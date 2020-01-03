<template>
  <div class="dashboard-container">
    <!-- <div class="app-container">目录管理</div> -->
    <el-card>
      <el-row slot="header">
        <el-button type="primary" @click="elasticLayer">新增目录</el-button>
        <el-button type="primary">返回学科</el-button>
      </el-row>

      <!-- 目录搜索 -->
      <el-row type="flex" align="middle" class="directory-search">
        <span class="directory-name">目录名称</span>
        <el-input style="width:20%" class="search-box" v-model="searchForm.directoryName"></el-input>
        <span class="directory-name">状态</span>
        <el-select style="width:20%" class="search-box" v-model="searchForm.state">
          <el-option
            v-for="item in status"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
        <el-button @click="clear">清除</el-button>
        <el-button type="primary" @click="search">搜索</el-button>
      </el-row>
      <!-- 列表 -->
      <el-table :data="list">
        <el-table-column label="序号" prop="id"></el-table-column>
        <el-table-column label="目录名称" prop="directoryName"></el-table-column>
        <el-table-column label="创建者" prop="username"></el-table-column>
        <el-table-column label="创建日期" prop="addDate">
          <!-- 作用域插槽 -->
          <!-- 过滤器 -->
          <template slot-scope="obj">{{obj.row.addDate | parseTimeByString}}</template>
        </el-table-column>
        <el-table-column label="面试题数量" prop="totals"></el-table-column>
        <el-table-column label="状态" prop="state" :formatter="formatterStatus"></el-table-column>
        <el-table-column label="操作" prop="creatorID">
          <!-- 作用域插槽 -->
          <template slot-scope="obj">
            <el-link type="primary" class="operation" @click="modify(obj.row.id)">修改</el-link>
            <el-link type="primary" class="operation" @click="disabledDirect(obj.row)">{{obj.row.state? '禁用' : '启用'}}</el-link>
            <el-link type="primary" class="operation" @click="deleteDirect(obj.row.id)">删除</el-link>
            </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-row type="flex" justify="end" align="middle" style="height:60px">
        <span>共{{page.total}}条</span>
        <el-pagination
          background
          layout="prev, pager, next"
          :total="page.total"
          :current-page="currentPage"
          :page-size="pageSize"
          @current-change="paging"
        ></el-pagination>
      </el-row>
      <!-- 弹层 -->
      <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
        <!-- 表单校验 -->
        <el-form label-width="100px" :model="ruleForm" :rules="rules" ref="myForm">
          <el-form-item label="目录名称" prop="directoryName">
            <el-input v-model="ruleForm.directoryName"></el-input>
          </el-form-item>
          <el-form-item label="学科" prop="subjectID">
            <el-select placeholder="请选择" v-model="ruleForm.subjectID">
              <el-option
                v-for="item in subjects"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-form>

        <el-row slot="footer" type="flex" justify="end">
          <el-button type="primary" @click="btnOK">确 定</el-button>
          <el-button @click="btnCancle">取 消</el-button>
        </el-row>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
import {
  list as DirectorysList,
  add as DirectorysAdd,
  remove as DirectorysRemove,
  removeState as changeState,
  update as updateDirectorys,
  detail as DirectorysDetail
} from "../../api/hmmm/directorys.js"; //目录列表、目录添加
import { simple as SubjectsSimple } from "../../api/hmmm/subjects.js"; //学科简单列表
import { status } from "../../api/hmmm/constants.js"; //状态
export default {
  name: "DirectorysList",
  data() {
    return {
      list: [], // 接收 列表数据
      page: {
        total: 0,
        currentPage: 1,
        pageSize: 8
      },
      dialogVisible: false, //弹层 默认隐藏
      // 校验
      ruleForm: {
        directoryName: "", //目录名称
        subjectID: "" ,//学科ID
      },
      //校验规则
      rules: {
        directoryName: [
          { required: true, message: "目录名称不能为空", trigger: "blur" },
          { min: 2, max: 7, message: "长度在 2 到 7 个字符", trigger: "blur" }
        ],
        subjectID: [
          { required: true, message: "学科不能为空", trigger: "change" }
        ]
      },
      //下拉菜单(弹层)
      subjects: [], //目录分类
      //带条件的搜索
      searchForm: {
        directoryName: "", //目录名称
        state: null //状态
      },
      //下拉状态
      status: status
    };
  },
  methods: {
    //修改
    modify(id){
      // 弹层
      this.dialogVisible = true //直接取反
      DirectorysDetail({id}).then(result=>{
        this.ruleForm=result.data
      })
    },
    //禁用
    disabledDirect(row){
      // alert(1)
      changeState({id:row.id,state:!row.state}).then(()=>{
          // 重新加载的时候 条件丢了不合适
        this.getDirectoryList(this.searchForm)
      })
    },
    //定义删除方法
    deleteDirect(id){
      this.$confirm('确定删除此目录吗').then(()=>{
        // 调用删除的接口
        DirectorysRemove({id}).then(()=>{
          this.$message({
            type:'success',
            message:'删除成功'
          })
          // 重新加载的时候 条件丢了不合适
        this.getDirectoryList(this.searchForm)
      })
      })
    },
    //分页
    paging(newPage){
      this.page.currentPage = newPage
      this.getDirectoryList(this.searchForm)
    },
    //清除
    clear() {
      this.searchForm = {
        directoryName: "", //目录名称
        state: null //状态
      };
    },
    //搜索
    search(){
      this.getDirectoryList(this.searchForm)
    },
    //状态
    formatterStatus(row, column, cellValue, index) {
      // status是数组
      // let result = this.status.filter(item => item.value === cellValue);
      // return result.length ? status[0].label : "未知状态";
      return cellValue?'启用':'禁用'
      
    },
    //确定cell
    btnOK() {
      //首先校验是否为空
      this.$refs.myForm.validate(async isOK => {
        if (isOK) {
          this.ruleForm.id
          ?await updateDirectorys(this.ruleForm)//ruleForm有id
          :await DirectorysAdd(this.ruleForm)//ruleForm没id
            this.ruleForm = {
              directoryName: "", //目录名称
              subjectID: "" //学科
            };
            this.dialogVisible = false; //关闭弹层
            //重新加载
            this.getDirectoryList(this.ruleForm); 
        }
      });
    },
    //取消
    btnCancle() {
      this.ruleForm = {
        directoryName: "", //目录名称
        subjectID: "" //学科
      };
      this.dialogVisible = false; //关闭弹层
    },
    //学科简单列表
    getSubjectsList() {
      SubjectsSimple().then(result => {
        console.log(result);
        this.subjects = result.data;
      });
    },
    //获取 目录列表
    getDirectoryList(data) {
      DirectorysList({
        page:this.page.currentPage,
        pagesize:this.page.pageSize,
        ...data
      }).then(result => {
        // console.log(result.data);
        // console.log(result.data.items);
        this.list = result.data.items; //列表数据
        this.page.total = result.data.counts; //总页数
      });
    },
    //新增目录 出现 弹层
    elasticLayer() {
      this.dialogVisible = !this.dialogVisible; //直接取反
    }
  },
  created() {
    this.getDirectoryList(); //获取 目录列表
    this.getSubjectsList();
  }
};
</script>

<style lang='scss' scoped>
.directory-search {
  margin-top: 20px;
  height: 60px;
  .directory-name {
    font-size: 14px;
    font-weight: bold;
    color: #666;
  }
  .search-box {
    margin: 0 10px;
  }
}
.operation {
  font-size: 12px;
  margin: 0 5px;
}
.subject {
  margin-top: 20px;
}
.frame {
  font-size: 14px;
  font-weight: bold;
  color: #666;
}
</style>
