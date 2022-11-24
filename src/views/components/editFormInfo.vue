<template>

  <el-dialog
      :visible.sync="dialogVisible"
      width="50%"
      :append-to-body="true"
      >

    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="参数" name="first">
        <div class="flex-container">
<!--          <el-tag>参数</el-tag>-->
          <div>
            <el-button size="mini" @click="addParmas" type="primary">新增</el-button>
<!--            <el-button size="mini" @click="useParmas" type="">配置</el-button>-->
            <el-button size="mini" @click="delParmas" type="danger">删除</el-button>
          </div>
        </div>

        <el-table
            ref="table"
            :data="topTable"
            style="width: 100%"
            @selection-change="handleSelectionChange"
        >
<!--          <el-table-column
              type="selection"
              width="55">
          </el-table-column>-->
          <el-table-column
              prop="name"
              label="参数名称"
              width="180">
          </el-table-column>
          <el-table-column
              prop="expression"
              label="判断条件"
              width="180">
          </el-table-column>
          <el-table-column
              prop="value"
              label="判定值">
          </el-table-column>
        </el-table>


      </el-tab-pane>
      <el-tab-pane label="中转数据" name="second">
        <div class="flex-container">
<!--          <el-tag>中转数据</el-tag>-->
          <div>
            <el-button size="mini" type="primary">新增</el-button>
            <el-button size="mini" type="danger">删除</el-button>
          </div>
        </div>
        <el-table
            ref="table"
            :data="bottomTable"
            style="width: 100%"
        >
          <el-table-column
              prop="name"
              label="参数名称"
              width="180">
          </el-table-column>
          <el-table-column
              prop="expression"
              label="判断条件"
              width="180">
          </el-table-column>
          <el-table-column
              prop="value"
              label="判定值">
          </el-table-column>
        </el-table>
      </el-tab-pane>
    </el-tabs>


<!-- 分割线  -->





<!--    <span slot="footer" class="dialog-footer">
    <el-button @click="cancel">取 消</el-button>
    <el-button type="primary" @click="submit">确 定</el-button>
  </span>-->
    <addParmas ref="addParmasRef" @add="addParmastoList"></addParmas>
  </el-dialog>
</template>

<script>
import addParmas from "@/views/components/addParmas";
export default {
name: "editFormInfo",
  data(){
    return {
      dialogVisible:false,
      topTable:[],
      bottomTable:[],
      activeName:'first',
      selectParames:[]
    }
  },
  components:{
    addParmas
  },
  methods:{
  init(){
    this.dialogVisible = true
    },

    addParmas(){
      this.$refs.addParmasRef.init()
    },
    addParmastoList(data){
    this.topTable.push(data) // 追加到table中
    this.$emit('useParma', this.topTable) // 更新节点信息
    },
    handleSelectionChange(val){
    this.selectParames=val
    },
   /* useParmas(){
    if(this.selectParames.length!==1){
      this.$message.warning('只能使用一项参数')
    }else{
      // todo: 逻辑代码
      const data = this.selectParames
      this.$emit('useParma',data)
      this.$message.success('使用成功')
      this.dialogVisible = false
    }
    },*/
    delParmas(){
      const delList = this.selectParames.map(i=>i.name)
      const dataList =  this.topTable
      delList.forEach((d)=>{
        dataList.forEach((i,index)=>{
          if(d === i.name){
            this.topTable.splice(index,1)
          }
        })
      })
    },
    handleClick(tab, event) {
      // console.log(tab, event);
    }
  }
}
</script>

<style scoped>
::v-deep .el-dialog__body{
  height: 500px;
}
</style>
