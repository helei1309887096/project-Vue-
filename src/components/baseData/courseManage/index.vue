<!-- 课程管理页面 -->
<template>
  <div class="courseManage">
    <div class="optionDiv">
      <el-input v-model="search" placeholder="请输入内容" size="mini"></el-input>
      <el-button @click="batchDeleteCourse" size="mini" type="success">批量删除</el-button>
      <el-button size="mini" type="success" @click="addCourse">新增</el-button>
    </div>
    <div class="tableDiv">
      <el-table
        :data="courseList"
        style="width: 100%;height:500px"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="55">
        </el-table-column>
        <el-table-column align="center"
          prop="name"
          label="课程名称">
        </el-table-column>
        <el-table-column align="center"
          prop="period"
          label="课程周期">
        </el-table-column>
        <el-table-column align="center"
          prop="description"
          label="课程简介">
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
        <el-form-item label="课程名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="课程周期" :label-width="formLabelWidth">
          <el-input v-model.number="form.period" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="课程介绍" :label-width="formLabelWidth">
          <el-input v-model="form.description" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false" size="mini">取 消</el-button>
        <el-button type="primary" size="mini" @click="saveCourse('ruleForm')">保 存</el-button>
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
      dialogTitle:'新增',
      dialogFormVisible:false,
      formLabelWidth:'80px',
      form:{
   
      },
      rules:{
      	name:[
          { required: true, message: '请输入课程名称', trigger: 'blur' }
        ],
        description:[
          { required: true, message: '请输入课程简介', trigger: 'blur' }
        ],
        period:[
		      { required: true, message: '请输入课程周期',trigger: 'blur'},
		      { type: 'number', message: '课程周期必须为数字值',trigger: 'blur'}
		    ],
      },
    }
  },
  created(){
  	this.findAllCourse();
  },
  computed:{
  	courseList(){
  		// courses进行搜索条件的过滤
  		let vm = this;
  		return this.courses.filter((item)=>{
  			if(item.name){
  				return item.name.indexOf(vm.search)!=-1;
  			}else{
  				return false;
  			}
  		});
  	},
  	...mapGetters(['courses']),
  },
  methods:{
    // 批量删除
    batchDeleteCourse(){
      // 获取用户选中的复选框对应的对象的id
      let ids = this.multipleSelection.map((item)=>{
        return item.id;
      });
      this.batchCourse({ids:ids.join(',')}).then((data)=>{
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '批量删除成功',
            type: 'success'
          });
          this.findAllCourse().then((data)=>{
            this.courseList = data;
          });
        }else{
          this.$notify.error({
            title: '失败',
            message: '批量删除失败',
            type: 'success'
          });
        }
      }).catch((error)=>{
        this.$notify.error({
          title: '失败',
          message: '批量删除失败',
          type: 'success'
        });
      });
    },
    // 保存
    saveCourse(){
      this.saveOneCourse(this.form).then((data)=>{
        console.log(data);
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '保存成功',
            type: 'success'
          });
          this.dialogFormVisible = false;
          this.findAllCourse().then((data)=>{
            this.gradeList = data;
          });
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
    addCourse(){
    	this.dialogTitle = '新增';
      this.form = {

      };
      this.dialogFormVisible = true;
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    handleEdit(index,row){
    	this.dialogTitle = '编辑';
      this.form = {
        description:row.description,
        name:row.name,
        period:row.period,
        id:row.id
      };
      this.dialogFormVisible = true;
    },
    handleDelete(index,row){
      this.deleteCourseById({id:row.id}).then((data)=>{
        if(data.stauts==200){
          this.$notify({
            title: '成功',
            message: '删除成功',
            type: 'success'
          });
          this.findAllCourse().then((data)=>{
            this.courseList = data;
          });
        }else{
          this.$notify.error({
            title: '失败',
            message: '删除失败',
            type: 'error'
          });
        }
      }).catch((error)=>{
        this.$notify.error({
          title: '失败',
          message: '删除失败',
          type: 'error'
        });
      });
    },
  	...mapActions(['findAllCourse','saveOneCourse','deleteCourseById','batchCourse']),
  }

}
</script>
<style>
  .optionDiv .el-input{
    width:200px;
  }
</style>
<style scoped lang="scss">
  .courseManage{
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