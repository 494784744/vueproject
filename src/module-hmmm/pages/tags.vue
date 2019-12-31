<template>
  <el-card style="  background-color: #e6f4ff">
    <el-form label-width='100px' >
      <el-form-item>
          <el-button type='primary' style="margin-left:-100px">新增标签</el-button>
          <el-button type='primary'>返回学科</el-button>
      </el-form-item>
      <el-card style="height:85px">
        <el-form-item label='标签名称' style="margin-left:-25px" >
          <el-select style="width:20%"></el-select>
          <el-button style="margin-left:10px">清除</el-button>
          <el-button type='primary'>搜索</el-button>
        </el-form-item>
      </el-card>
      <el-card style="margin-top:30px">
        <el-for-item>
          <el-table>
            <el-table-column label='序号'></el-table-column>
            <el-table-column label='标签名称'></el-table-column>
            <el-table-column label='创建者'></el-table-column>
            <el-table-column label='面试题数量'></el-table-column>
            <el-table-column label='状态'></el-table-column>
            <el-table-column label='操作'></el-table-column>
          </el-table>
        </el-for-item>
      </el-card>
    </el-form>
  </el-card>
</template>

<script>

export default {
  name: 'TagsList',
  data() {
    return {}
  }
}
</script>

<style lang='less' scoped>

</style>
