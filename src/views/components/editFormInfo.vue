<template>

  <el-dialog
      :visible.sync="dialogVisible"
      width="50%"
      :append-to-body="true"
      >
    <el-form ref="info_form_ref"
             :model="info_form" label-width="80px"
    >
      <el-form-item
          label="节点名称"
          prop="nodeName"
          :rules="[{ required: true, message: '请输入节点名称', trigger: 'blur' }]"
      >
        <el-input style="width:300px" v-model="info_form.nodeName"></el-input>
      </el-form-item>
    </el-form>

    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="参数" name="first">
        <div class="flex-container">
<!--          <el-tag>参数</el-tag>-->
          <div>
            <el-button size="mini" @click="addParmas" type="primary">新增</el-button>
            <el-button size="mini" @click="editParmas" type="">编辑</el-button>
            <el-button size="mini" @click="delParmas" type="danger">删除</el-button>
          </div>
        </div>

        <el-table
            ref="table"
            :data="topTable"
            style="width: 100%;height: 240px; overflow: auto"
            @selection-change="handleSelectionChange"
        >
          <el-table-column
              type="selection"
              width="55">
          </el-table-column>
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
            <el-button size="mini" @click="addTransit" type="primary">新增</el-button>
            <el-button size="mini" @click="delTransit" type="danger">删除</el-button>
          </div>
        </div>


        <el-table ref="table"
                  :data="bottomTable"
                  style="width: 100%;height: 240px; overflow: auto"
                  @selection-change="handleSelectionChange_transit"
        >

          <el-table-column
              type="selection"
              width="55">
          </el-table-column>

          <el-table-column min-width="150px" label="客户关系表">
            <template slot-scope="{row}">
              <template v-if="row.edit">
                <el-input v-model="row.customer" class="edit-input" size="small" />
              </template>
              <span v-else>{{ row.customer }}</span>
            </template>
          </el-table-column>

          <el-table-column min-width="150px" label="中转表">
            <template slot-scope="{row}">
              <template v-if="row.edit">
                <el-input v-model="row.transfer" class="edit-input" size="small" />
              </template>
              <span v-else>{{ row.transfer }}</span>
            </template>
          </el-table-column>

          <el-table-column align="center" label="操作" width="120">
            <template slot-scope="{row}">
              <el-button
                  v-if="row.edit"
                  type="success"
                  size="small"
                  icon="el-icon-check"
                  @click="confirmEdit(row)"
              >
<!--                Ok-->
              </el-button>
              <el-button
                  v-else
                  type="primary"
                  size="small"
                  icon="el-icon-edit"
                  @click="row.edit=!row.edit"
              >
<!--                Edit-->
              </el-button>
            </template>
          </el-table-column>
        </el-table>



      </el-tab-pane>
    </el-tabs>


<!-- 分割线  -->





    <span slot="footer" class="dialog-footer">
    <el-button @click="cancel">取 消</el-button>
    <el-button type="primary" @click="submit">确 定</el-button>
  </span>
    <parmasFormInfo ref="parmasFormInfoRef" @add="addParmastoList" @edit="editParmastoList"></parmasFormInfo>
  </el-dialog>
</template>

<script>
import parmasFormInfo from "@/views/components/parmasFormInfo";
import select from "view-design/src/components/select";
export default {
name: "editFormInfo",
  data(){
    return {
      dialogVisible:false,
      topTable:[],
      bottomTable:[],
      activeName:'first',
      selectParames:[], // 批量操作参数表的数据
      selectTransitions:[], // 批量操作中转数据表的数据
      info_form:{
        nodeName:'',
        parmasList:[], // 参数表数据
        transitList:[], // 中转数据
      }
    }
  },
  components:{
    parmasFormInfo
  },
  methods:{
  init(data){
    this.dialogVisible = true
    if(data){ // 如果data有值，初始化数据
      this.info_form.name = data.nodeName || ''
      this.topTable = data.parmasList || []
      this.bottomTable = data.transitList || []
    }

    },

    addParmas(){
      this.$refs.parmasFormInfoRef.init('add')
    },
    addParmastoList(data){
    this.topTable.push(data) // 追加到table中
    },
    editParmastoList(data){ // 表单中修改的数据，需要传到列表中
      this.topTable.forEach(i=>{
        if (i.id === data.id){
          i = data
        }
      })
    },
    addTransit(){
     // 追加一行
      const data = {
        customer:'',
        id:'',
        transfer:''
      }
      this.$set(data, 'edit', true)
      this.bottomTable.push(data)
    },
    delTransit(){
      const delList = this.selectTransitions.map(i=>i.name)
      const dataList =  this.bottomTable
      this.del(delList,dataList,'transfer')
    },
    // 中转数据表 行内数据保存
    confirmEdit(row){
      row.edit = false
    },//


    handleSelectionChange(val){
    this.selectParames=val
    },
    handleSelectionChange_transit(val){
    this.selectTransitions = val
    },
    editParmas(){
    if(this.selectParames.length!==1){
      this.$message.warning('一次只能编辑一项参数')
    }else{
      // todo: 逻辑代码
      const data= this.selectParames[0] // 选中，需要编辑的那一项
      this.$refs.parmasFormInfoRef.init('edit',data)

    }
    },
    del(delList,dataList,type){
      delList.forEach((d)=>{
        dataList.forEach((i,index)=>{
          if(d === i.name){
            if(type === 'parmas'){
              this.topTable.splice(index,1)
            }else {
              this.bottomTable.splice(index,1)
            }

          }
        })
      })
    },
    delParmas(){
      const delList = this.selectParames.map(i=>i.name)
      const dataList =  this.topTable
      this.del(delList,dataList,'parmas')
    },
    cancel(){
      this.dialogVisible = false
    },
    submit(){
      this.$refs.info_form_ref.validate(v=>{
        console.log(v);
        if(v){
          this.info_form.parmasList = this.topTable
          this.info_form.transitList = this.bottomTable
          this.$emit('setNodeData', this.info_form) // 更新节点信息
          this.dialogVisible = false
        }
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
  height: 420px;
}
</style>
