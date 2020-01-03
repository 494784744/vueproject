<template>
  <el-card class="subject">
        <!-- 新增学科第一部分 -->
        <el-row class="subOne">
          <el-button type="primary" @click="add">新增学科</el-button>
          <!--对话框里的内容-->
          <el-dialog :visible="dialogVisble" :before-close="handleClose">
            <el-form ref="ruleForm" :rules="rules" :model="addForm">
              <!-- 校验 -->
              <el-form-item
                hide-required-asterisk="true"
                class="addContent"
                label="学科名称"
                prop="subjectName"
              >
                <el-input v-model="addForm.subjectName" class="addname"></el-input>
              </el-form-item>

              <el-form-item class="addvisble" label="是否前台显示" prop="isFrontDisplay">
                <el-switch
                  v-model="addForm.isFrontDisplay"
                  active-color="dodgerblue"
                  inactive-color="#eee6e6"
                ></el-switch>
              </el-form-item>
              <el-row style="margin-top:50px" type="flex" justify="end">
                <el-button @click="submitForm" type="primary">确定</el-button>
                <el-button @click="cancelSubject">取消</el-button>
              </el-row>
            </el-form>
          </el-dialog>
        </el-row>

        <!-- 筛选第二部分 -->
        <el-row class="subTwo" label-width="100px">
          <el-form>
            <el-form-item hide-required-asterisk="true" class="addContent" label="学科名称">
              <el-input v-model="searchland" class="subinput"></el-input>
              <el-button @click="removeContent">清除</el-button>
              <el-button type="primary" @click="searchSubject">搜索</el-button>
            </el-form-item>
          </el-form>
        </el-row>

        <!--表格第三部分-->
        <el-row class="subThree">
          <el-table :data="list" style="width: 100%">
            <el-table-column class="list" prop="id" label="序号" width="120"></el-table-column>
            <el-table-column class="list" prop="subjectName" label="学科名称" width="120"></el-table-column>
            <el-table-column class="list" prop="username" label="创建者" width="120"></el-table-column>
            <el-table-column class="list" prop="addDate" label="创建日期" width="220"></el-table-column>
            <el-table-column
              class="list"
              prop="isFrontDisplay"
              label="前台是否显示"
              width="120"
              :formatter="boolchange"
            ></el-table-column>
            <el-table-column class="list" prop="twoLevelDirectory" label="二级目录" width="120"></el-table-column>
            <el-table-column class="list" prop="tags" label="标签" width="120"></el-table-column>
            <el-table-column class="list" prop="totals" label="题目数量" width="120"></el-table-column>
            <el-table-column class="list" label="操作" width="240">
              <!-- 删除数据 -->
              <template slot-scope="obj">
                <el-link type="primary">学科分类</el-link>
                <el-link class="subTab" type="primary">学科标签</el-link>
                <el-link @click="edit(obj.row.id)" class="subUpdate" type="primary">修改</el-link>

                <el-link @click="delSubject(obj.row.id)" class="subDel" type="primary">删除</el-link>
              </template>
            </el-table-column>
          </el-table>
        </el-row>

        <!-- 分页第四部分 -->
        <el-row type="flex" justify="end" class="subFour">
          <span style="margin-right:10px;margin-top:7px;">共{{page.total}}条</span>
          <el-pagination
            @current-change="Page"
            background
            layout="prev, pager, next"
            :total="page.total"
            :page-size="page.pageSize"
            :current-page="page.currentPage"
          ></el-pagination>
        </el-row>
  </el-card>
</template>

<script>
//  学科
import {
  list as subjectList,
  add as addSubject,
  remove as delSub,
  update as updateSub,
  detail as updateDetail
} from "../../../src/api/hmmm/subjects";
import { log } from "util";
export default {
  name: "SubjectsList",
  data() {
    return {
      edittype: true,
      //搜索框
      searchland: "",

      //提示框名字校验
      addForm: {
        subjectName: "",
        isFrontDisplay: false
      },
      //定义是否前台显示默认值
      // value: false,
      //定义一个空数组
      list: [],
      //分页
      page: {
        total: 1,
        pageSize: 8,
        currentPage: 1
      },
      //对话框显示
      dialogVisble: false,
      rules: {
        subjectName: [
          { required: true, message: "表单内容不能为空", trigger: "blur" }
        ]
      }
    };
  },
  methods: {
    add() {
      this.edittype = true;
      this.dialogVisble = true;
      this.addForm = {
        subjectName: "",
        isFrontDisplay: false
      };
    },
    //修改数据
    edit(id) {
      this.edittype = false;
      this.dialogVisble = true;
      updateDetail({ id }).then(res => {
        // console.log(res.data.isFrontDisplay);
        this.addForm = res.data;
        this.addForm.isFrontDisplay =
          res.data.isFrontDisplay === 0 ? false : true;
      });
    },

    //删除数据
    delSubject(id) {
      this.$confirm("您确定要删除吗").then(res => {
        if (res) {
          delSub({ id }).then(() => {
            // alert("删除成功");
          });
          this.getList();
        }
      });
    },

    //搜索名称
    searchSubject() {
      this.getList();
    },

    //搜索清空
    removeContent() {
      this.searchland = "";
    },

    //取消弹框
    cancelSubject() {
      this.dialogVisble = false;
    },
    //是否显示在前台
    boolchange(row, column, cellvalue, index) {
      return cellvalue ? "显示" : "-";
    },

    //新增和修改学科
    submitForm() {
      if (this.edittype) {
        this.$refs.ruleForm.validate(isOK => {
          if (isOK) {
            // console.log("校验成功");
            //新增接口
            addSubject(this.addForm).then(res => {
              //  console.log(res);
              //更新
              this.getList();
              //内容清空
              this.addForm.subjectName = "";
              //对话框消失
              this.dialogVisble = false;
            });
          }
        });
      } else if (this.edittype === false) {
        this.$refs.ruleForm.validate(isOK => {
          if (isOK) {
            // console.log("校验成功");
            //新增接口
            updateSub(this.addForm).then(res => {
              //  console.log(res);
              //更新
              this.getList();
              //内容清空

              //对话框消失
              this.dialogVisble = false;
            });
          }
        });
      }
    },

    //关闭对话框
    handleClose() {
      this.dialogVisble = !this.dialogVisble;
    },

    //设置分页数据
    Page(newpage) {
      this.page.currentPage = newpage;
      console.log(this.page.currentPage);
      //重新更新列表
      this.getList();
    },

    //获取列表
    getList() {
      //更新数据
      subjectList({
        pagesize: this.page.pageSize,
        page: this.page.currentPage,
        //搜索获取名字
        subjectName: this.searchland
      }).then(res => {
        // console.log(res.data.items);
        //赋值
        this.list = res.data.items;
        // console.log(res.data.counts);
        // 获取全部
        this.page.total = res.data.counts;
      });
    }
  },
  created() {
    this.getList();
  }
};
</script>

<style lang='scss' scoped>
.subject {
  //第一部分
  .subOne {
    margin-top: 10px;
    padding-bottom: 20px;
    border-bottom: 1px solid #eee6e6;
    // color:dodgerblue;
    .addContent {
      font-weight: 900;
      margin-left: 40px;
      .addname {
        width: 70%;
        margin-left: 10px;
      }
    }
    .addvisble {
      margin-top: 30px;
      font-weight: 900;
      margin-left: 44px;
      span {
        margin-right: 10px;
      }
    }
  }

  //第二部分
  .subTwo {
    margin-top: 20px;
    .subinput {
      width: 20%;
      margin-right: 10px;
    }
    .subname {
      margin-right: 10px;
    }
  }

  //第三部分
  .subThree {
    margin-top: 15px;
    .list {
      padding-bottom: 20px;
    }
    .subUpdate {
      margin-left: 10px;
    }
    .subTab {
      margin-left: 10px;
    }
    .subDel {
      margin-left: 10px;
    }
  }

  //第四部分
  .subFour {
    margin-top: 20px;
  }
}
</style>
