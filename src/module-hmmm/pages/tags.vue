<template>
  <el-card style="background-color: #e6f4ff">
    <el-row slot="header" style="margin-top:-5px">
      <el-button type="primary" @click="dialogVisible=true">新增标签</el-button>
      <el-button type="primary" @click="$router.push('/subjects/list?id=123')">返回学科</el-button>
    </el-row>
    <el-form :model="tagsForm" :rules="rules" label-width="100px">
      <el-card style="height:82px;margin-top:-23px">
        <el-form-item label="标签名称" prop="tagName" placeholder="请输入标签名称" style="margin-left:-25px">
          <el-input v-model="tagsForm.tagName" style="width:20%"></el-input>
          <el-button @click="cleanTags" style="margin-left:10px">清除</el-button>
          <el-button @click="searchTags" type="primary">搜索</el-button>
        </el-form-item>
      </el-card>
    </el-form>

    <el-card style="margin-top:18px">
      <el-table :data="list">
        <el-table-column label="序号" prop="id" align="center"></el-table-column>
        <el-table-column label="标签名称" prop="tagName" align="center"></el-table-column>
        <el-table-column label="创建者" prop="creator" align="center"></el-table-column>
        <el-table-column label="创建日期" prop="addDate" align="center" width="200">
          <!-- 作用域插槽 row(行数据) column $index store-->
          <!-- 过滤器日期时间转化 -->
          <template slot-scope="Obj">{{Obj.row.addDate|parseTimeByString}}</template>
        </el-table-column>
        <el-table-column label="面试题数量" prop="totals" align="center"></el-table-column>
        <el-table-column label="状态" prop="state" align="center" :formatter="formatterBoolean"></el-table-column>
        <el-table-column label="操作" align="center">
          <!-- 作用域插槽 -->
          <template slot-scope="Obj">
            <el-button type="text" @click="modifyTags(Obj.row.id)">修改</el-button>
            <!-- 穿过整条数据到放法 -->
            <el-button type="text" @click="changeState(Obj.row)">{{Obj.row.state === 1? '禁用' : '启用'}}</el-button>
            <el-button type="text" @click="delTags(Obj.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog :visible.sync="dialogVisible" :show-close="false">
        <el-form ref="myTagsForm" :model="subjectForm" :rules="rules" label-width="100px">
          <el-form-item label="学科名称" prop="tagName" placegolder="请输入学科名称">
            <el-input v-model="subjectForm.tagName" style="width:45%"></el-input>
          </el-form-item>
          <el-form-item label="学科" prop="subjectID">
            <el-select v-model="subjectForm.subjectID" style="width:30%">
              <el-option
                v-for="item in subjectsOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <span slot="footer">
          <el-button type="primary" @click="btnOk">确定</el-button>
          <el-button @click="btnCancel">取消</el-button>
        </span>
      </el-dialog>
      <el-pagination
        @current-change="changeCurrent"
        :page-size="page.pageSize"
        :current-page="page.currentPage"
        :total="page.total"
        background
        layout="prev,pager,next">
      </el-pagination>
    </el-card>
  </el-card>
</template>

<script>
import { status } from "../../api/hmmm/constants"
import { simple as simpleSubjects } from "../../api/hmmm/subjects"
import { add,update,detail,remove,removeState,list as tagsList } from "../../api/hmmm/tags"

export default {
  name: "TagsList",
  data() {
    return {
      status, //相当于把status数据给data中变量
      list: [], //目录列表数据,用来绑定给表格
      dialogVisible: false, //对话框默认关闭
      subjectsOptions: [], //学科数据
      // 分页数据
      page: {
        total: 0,
        pageSize: 10,
        currentPage: 1
      },
      // 标签表单数据
      tagsForm: {
        state: null,
        tagName: ""
      },
      // 学科表单数据
      subjectForm: {
        tagName: "", //标签名称
        subjectID: null //学科id
      },
      // 校验规则
      rules: {
        tagName: [
          { required: true, message: "内容不能为空", trigger: "blur" },
          { min: 1, max: 12, message: "长度在1到12个字符", trigger: "blur" }
        ],
        subjectID: [
          { required: true, message: "学科不能为空", trigger: "blur" }
        ]
      }
    }
  },
  methods: {
     searchTags(){
      this.page.currentPage=1
      this.getTagsList(this.tagsForm)
    },
    cleanTags(){
      this.tagsForm={
        state: null,
        tagName: ""
      }
      this.getTagsList()
    },
    changeCurrent(newPage) {
      this.page.currentPage=newPage
      this.getTagsList()
    },
    // 修改标签
    async modifyTags(id) {
      this.dialogVisible = true; //一进来打开对话框
      let result = await detail({ id }) //获取当前要修改的数据
      this.subjectForm = result.data
    },
    // 添加标签
    btnOk() {
      this.$refs.myTagsForm.validate(async isOk => {
        if (isOk) {
          // 有id修改,没有id添加
          this.subjectForm.id ? await update(this.subjectForm) : await add(this.subjectForm)
          // 新增标签完后,清空数据
          this.subjectForm = {
            subjectID: null,
            tagName: ""
          }
        }
        this.dialogVisible = false
        this.getTagsList(this.tagsForm)
      })
    },
    // 点击取消对话框,并清空数据
    btnCancel() {
      this.dialogVisible = false
      this.subjectForm = {
        subjectID: null,
        tagName: ""
      }
    },
    // 删除标签
    async delTags(row) {
      await this.$confirm("你是否需要删除该标签", "提示", { type: "warning" })
      await remove({ id: row.id })
      this.$message({ type: "success", message: "删除成功" })
      this.getTagsList()
    },
    // 启用或者关闭标签
    async changeState(row) {
      await removeState({ id: row.id, state: row.state === 1 ? 0 : 1 }) //状态取反
      this.$message({ type: "success", message: "修改状态成功" })
      this.getTagsList()
    },
    // 将状态码布尔值改为方法
    formatterBoolean(row, column, cellValue, index) {
      // status中找到匹配的value 返回对应的label
      let result = this.status.filter(item => {
        if (item.value === cellValue) {
          return item
        }
      })
      return result.length ? result[0].label : "未知状态"
    },
    // 获取标签列表数据
    async getTagsList(data) {
      let res = await tagsList({
        page:this.page.currentPage, //默认请求第一页
        pagesize:this.page.pageSize, //请求的每页多少条
        ...data
      })
      this.list = res.data.items
      this.page.total = res.data.counts
    },
    // 获取学科数据
    async getSubjects() {
      let res = await simpleSubjects()
      this.subjectsOptions = res.data
    }
  },
  created() {
    this.getTagsList()
    this.getSubjects()
  }
}
</script>

<style>
</style>
