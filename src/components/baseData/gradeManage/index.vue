<!-- 年级管理页面 -->
<template>
  <div class="gradeManage">
    <div class="optionDiv">
      <el-input v-model="search" placeholder="请输入内容" size="mini"></el-input>
      <el-button class="searchBtn" @click="toSearch" size="mini" type="success">搜索</el-button>
      <el-button @click="batchDeleteGrade" size="mini" type="success">批量删除</el-button>
      <el-button size="mini" type="success" @click="addGrade">新增</el-button>
    </div>
    <div class="tableDiv">
      <el-table
        :data="gradeList"
        style="width: 100%;height:500px"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="55">
        </el-table-column>
        <el-table-column align="center"
          prop="name"
          label="名称">
        </el-table-column>
        <el-table-column align="center"
          prop="descriptioin"
          label="简介">
        </el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <i class="fa fa-pencil-square-o" title="编辑" style="color:green;font-size:20px;" @click="handleEdit(scope.$index, scope.row)"></i>
            <i class="fa fa-trash-o" title="删除" style="color:red;font-size:20px;margin-left:10px" @click="handleDelete(scope.$index, scope.row)"></i>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="年级名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="年级简介" :label-width="formLabelWidth">
          <el-input v-model="form.descriptioin" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false" size="mini">取 消</el-button>
        <el-button type="primary" size="mini" @click="saveGrade">保 存</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import {mapGetters,mapActions} from 'vuex';
export default {
  data(){
    return {
      search:'',
      multipleSelection:[],
      gradeList:[],
      dialogTitle:'新增',
      dialogFormVisible:false,
      formLabelWidth:'80px',
      form:{
        schoolId:3
      }
    }
  },
  computed:{
  	...mapGetters(['msg','grades']),
  },
  created(){
    let vm = this;
  	this.findAllGrade().then(function(data){
      vm.gradeList = data;
    }).catch();
  },
  methods:{
    // 批量删除
    batchDeleteGrade(){
      // 获取用户选中的复选框对应的对象的id
      let ids = this.multipleSelection.map((item)=>{
        return item.id;
      });
      this.batchGrade({ids:ids.join(',')}).then((data)=>{
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '批量删除成功',
            type: 'success'
          });
          this.findAllGrade().then((data)=>{
            this.gradeList = data;
          });
        }else{
          this.$notify.error({
            title: '失败',
            message: '批量删除失败'
          });
        }
      }).catch((error)=>{
        this.$notify.error({
          title: '失败',
          message: '批量删除失败'
        });
      });
    },
    // 保存
    saveGrade(){
      this.saveOneGrade(this.form).then((data)=>{
        console.log(data);
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '保存成功',
            type: 'success'
          });
          this.dialogFormVisible = false;
          this.findAllGrade().then((data)=>{
            this.gradeList = data;
          });
        }else{
          this.dialogFormVisible = false;
          this.$notify.error({
            title: '失败',
            message: '保存失败'
          });
        }
      }).catch((error)=>{
        this.dialogFormVisible = false;
        this.$notify.error({
          title: '失败',
          message: '保存失败'
        });
      });
    },
    // 新增按钮
    addGrade(){
      this.form = {
        schoolId:3
      };
      this.dialogFormVisible = true;
    },
    toSearch(){
      let vm = this;
      this.gradeList = this.grades.filter(function(item){
        if(item.name){
          return item.name.indexOf(vm.search)!=-1;
        }else{
          return true;
        }
      });
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    handleEdit(index,row){
      this.form = {
        schoolId:3,
        name:row.name,
        comment:row.comment,
        id:row.id
      };
      this.dialogFormVisible = true;
    },
    handleDelete(index,row){
      this.deleteGradeById({id:row.id}).then((data)=>{
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '删除成功',
            type: 'success'
          });
          this.findAllGrade().then((data)=>{
            this.gradeList = data;
          });
        }else{
          this.$notify.error({
            title: '失败',
            message: '删除失败'
          });
        }
      }).catch((error)=>{
        this.$notify.error({
          title: '失败',
          message: '删除失败'
        });
      });
    },
  	...mapActions(['findAllGrade','saveOneGrade','deleteGradeById','batchGrade']),
  }

}
</script>
<style>
  .optionDiv .el-input{
    width:200px;
  }
</style>
<style scoped lang="scss">
  .gradeManage{
    .optionDiv{
      button{
        float:right;
        margin-left:10px;
      }
      .searchBtn{
        float:none;
        margin-left:3px;
      }
    }
    .tableDiv i{
      	cursor: pointer;
      }
  }
</style>