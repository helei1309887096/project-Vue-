<!-- 班级管理页面 -->
<template>
  <div class="clazzManage">
  <!-- {{classList}} -->
  <!-- {{teachers}} -->
    <div class="optionDiv">
      <el-select size="mini" v-model="choice.gradeId" clearable placeholder="请选择">
		    <el-option
		      v-for="(item,index) in grades" 
		      :key="item.index" 
		      :label="item.name"
		      :value="item.id">
		    </el-option>
		  </el-select>
      <el-input v-model="choice.search" placeholder="请输入内容" size="mini"></el-input>
      <el-button @click="batchDeleteClazz" size="mini" type="success">批量删除</el-button>
      <el-button size="mini" type="success" @click="addClazz">新增</el-button>
    </div>
    <div class="tableDiv">
      <el-table
        :data="clazzList"
        style="width: 100%;height:500px"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="55">
        </el-table-column>
        <el-table-column align="center"
          prop="name"
          label="班级名称">
        </el-table-column>
        <el-table-column align="center"
          prop="description"
          label="班级简介">
        </el-table-column>
        <el-table-column align="center"
          prop="charge.name"
          label="班主任">
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
        <el-form-item label="所属年级" :label-width="formLabelWidth">
		      <el-select v-model="form.gradeId" placeholder="请选择">
				    <el-option
					    v-for="(item,index) in grades" 
					    :key="item.index" 
					    :label="item.name"
					    :value="item.id">
				    </el-option>
					</el-select>
				</el-form-item>  
        <el-form-item label="班级名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="班级简介" :label-width="formLabelWidth">
          <el-input v-model="form.description" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="班主任" :label-width="formLabelWidth">
	        <el-select v-model="form.chargeId" placeholder="请选择">
				    <el-option
					    v-for="(item,index) in teachers" 
					    :key="item.index" 
					    :label="item.name"
					    :value="item.id">
				    </el-option>
				  </el-select>
				</el-form-item> 
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false" size="mini">取 消</el-button>
        <el-button type="primary" size="mini" @click="saveClazz">保 存</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import {mapGetters,mapActions} from 'vuex';
export default {
  data(){
    return {
    	choice:{
    		gradeId:'',
    		search:''
    	},
      multipleSelection:[],
      dialogTitle:'新增',
      dialogFormVisible:false,
      formLabelWidth:'80px',
      form:{
        schoolId:3
      }
    }
  },
  created(){
  	this.findAllClazz();
    this.findAllTeacher();
    this.findAllGrade();
  },
  computed:{
  	clazzList(){
  		let vm = this;
  		return this.clazzes.filter((item)=>{
  			if(item.name){
  				if(item.grade&&vm.choice.gradeId){
						return (item.name.indexOf(vm.choice.search)!=-1)&&(item.grade.id==vm.choice.gradeId);
  				}
  				return item.name.indexOf(vm.choice.search)!=1;
  			}else{
  				return false;
  			}
  		});
  	},
  	...mapGetters(['clazzes','teachers',"grades"]),
  },
  methods:{
    // 批量删除
    batchDeleteClazz(){
      // 获取用户选中的复选框对应的对象的id
      let ids = this.multipleSelection.map((item)=>{
        return item.id;
      });
      this.batchClazz({ids:ids.join(',')}).then((data)=>{
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '批量删除成功',
            type: 'success'
          });
          this.findAllClazz().then();
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
    saveClazz(){
      this.saveOneClazz(this.form).then((data)=>{
        console.log(data);
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '保存成功',
            type: 'success'
          });
          this.dialogFormVisible = false;
          this.findAllClazz().then();
        }else{
          this.dialogFormVisible = false;
          this.$notify.error({
            title: '失败',
            message: '保存失败',
            type: 'error'
          });
        }
      }).catch((error)=>{
        this.dialogFormVisible = false;
        this.$notify.error({
          title: '失败',
          message: '保存失败',
          type: 'error'
        });
      });
    },
    // 新增按钮
    addClazz(){
    	this.dialogTitle = '新增';
      this.form = {
        schoolId:3
      };
      this.dialogFormVisible = true;
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
      this.deleteClazzById({id:row.id}).then((data)=>{
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '删除成功',
            type: 'success'
          });
          this.findAllClazz().then();
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
  	...mapActions(['findAllClazz','saveOneClazz','deleteClazzById','batchClazz','findAllTeacher','findAllGrade']),
  }

}
</script>
<style>
  .optionDiv .el-input{
    width:200px;
  }
</style>
<style scoped lang="scss">
  .clazzManage{
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