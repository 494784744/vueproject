<template>
  <el-card class="question-container">
    <div slot="header">
      <el-row>
        <el-button type="primary" size="mini">新增试题</el-button>
        <el-button type="primary" size="mini">批量导入</el-button>
      </el-row>
    </div>
    <el-form :inline="true">
      <!-- 第一行 -->
      <el-row>
        <el-form-item label="学科">
          <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="难度">
            <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="试题类型">
          <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签">
          <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-row>
      <!-- 第二行 -->
      <el-row>
        <el-form-item label="城市">
          <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="区域">
         <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="关键字">
          <el-input v-model="input1" placeholder="请输入内容"></el-input>
        </el-form-item>
        <el-form-item label="题目备注">
          <el-input v-model="input1" placeholder="请输入内容"></el-input>
        </el-form-item>
      </el-row>
      <!-- 第三行 -->
      <el-row>
        <el-form-item label="企业简称">
          <el-input v-model="input1" placeholder="请输入内容"></el-input>
        </el-form-item>
        <el-form-item label="方向">
         <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="录入人">
         <el-select v-model="value" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级目录">
          <el-input v-model="input1" placeholder="请输入内容"></el-input>
        </el-form-item>
      </el-row>
    </el-form>
  </el-card>
</template>

<script>
export default {
  data() {
    return {
      input1: "",
       options: [{
          value: '选项1',
          label: '黄金糕'
        }, {
          value: '选项2',
          label: '双皮奶'
        }, {
          value: '选项3',
          label: '蚵仔煎'
        }, {
          value: '选项4',
          label: '龙须面'
        }, {
          value: '选项5',
          label: '北京烤鸭'
        }],
        value: ''
    };
  }
};
</script>

<style lang="scss">
</style>
