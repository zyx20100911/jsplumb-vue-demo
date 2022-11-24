<template>
  <el-dialog
      :title="title"
      v-if="dialogVisible"
      :visible.sync="dialogVisible"
      width="30%"
      :append-to-body="true"
      custom-class="addParmas"
  >
    <el-form ref="form" :model="form" label-width="80px">
      <el-row :gutter="20">
        <el-form-item label="参数名称">
        <el-col :span="24">
            <el-input v-model="form.name"></el-input>
        </el-col>
        </el-form-item>
      </el-row>
      <el-row  :gutter="20">

          <el-form-item label="判断条件">
            <el-col :span="8">
            <el-select v-model="form.expression" placeholder="请选择">
              <el-option label="等于=" value="="></el-option>
              <el-option label="不等于!=" value="!="></el-option>
              <el-option label="大于>" value=">"></el-option>
              <el-option label="小于<" value="<"></el-option>
              <el-option label="大于等于>=" value=">="></el-option>
              <el-option label="小于等于<=" value="<="></el-option>
              <el-option label="选项[]" value="选项[]"></el-option>
            </el-select >
            </el-col>
            <el-col :span="16">
              <el-input v-model="form.value"></el-input>
            </el-col>

          </el-form-item>
      </el-row>


    </el-form>
    <span slot="footer" class="dialog-footer">
    <el-button @click="cancel">取 消</el-button>
    <el-button type="primary" @click="submit">确 定</el-button>
  </span>
  </el-dialog>
</template>

<script>
import methods from "@/views/config/methods";
import {GenNonDuplicateID} from "@/common/until";

export default {
  name: "add",
  data() {
    return{
      dialogVisible:false,
      rules:{
        name:[{}]
      },
      form:{
        id:'',
        name:'',
        expression:'=', // 判断条件
        value:''
      },
      type:'',
      title:''
    }
  },
  methods:{
    init(type,data){
      this.dialogVisible = true
      this.type = type
      if(type === 'add'){
        this.title='新增参数'
        this.form={
          id:'',
          name:'',
          expression:'=', // 判断条件
          value:''
        }
      }
      if(type==='edit'){
        this.title='修改参数'
        this.form = data
      }
    },
    cancel(){
      this.dialogVisible = false
    },
    submit(){
      if(this.type==='add'){
        this.form.id = GenNonDuplicateID(6)
        const data = this.form
        this.$emit('add',data)
        this.dialogVisible = false
      }
      if(this.type==='edit'){
        const data = this.form
        this.$emit('edit',data)
        this.dialogVisible = false
      }

    }
  }
}
</script>

<style scoped>
::v-deep .addParmas .el-dialog__body{
  height: 150px;
}
</style>
