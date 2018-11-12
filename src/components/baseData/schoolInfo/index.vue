<!-- 学校信息页面 -->
<template>
  <div class="schoolInfo">
  	<table>
			<tr @click="show=false" v-for="(item,index) in tableArr">
				<td>{{item.name}}</td>
				<td>
					<span v-if="show">{{schoolInfo[item.prop]}}</span>
					<el-input v-else v-model="form[item.prop]" placeholder="请输入内容"></el-input>
				</td>
			</tr>
		</table>
  	<el-button type="primary" size="mini" @click="saveSchoolInfo">保 存</el-button>
  </div>
</template>

<script>
import {mapGetters,mapActions} from 'vuex';
export default {
  data(){
    return {
    	show:true,
    	tableArr:[{
     		name:"校园名称",
     		prop:'name'
     	},{
     		name:"校园介绍",
     		prop:'description'
     	},{
     		name:"校园地址",
     		prop:'address'
     	},{
     		name:"校园电话",
     		prop:'telephone'
     	},{
     		name:"版权信息",
     		prop:'copyright'
     	}],
     	form:{},
    }
  },
  created(){
  	this.findAllSchoolInfo({id:3}).then((data)=>{
  		this.form = {
  			id:data.id,
  			name:data.name,
  			description:data.description,
  			address:data.address,
  			telephone:data.telephone,
  			copyright:data.copyright,
  		};
  	}).catch(function(error){
        console.log(error)
    });
  },
  computed:{
  	// schoolInfoList(){
  	// 	let vm = this;
  	// 	return this.schoolInfo.filter((item)=>{
  	// 		if(item.name){
  	// 			return item.name.indexOf(vm.search)!=-1;
  	// 		}else{
  	// 			return false;
  	// 		}
  	// 	});
  	// },
  	...mapGetters(['schoolInfo'])
  },
  methods:{
  	saveSchoolInfo(){
  		this.saveOneSchoolInfo(this.form).then((data)=>{
  			if(data.stauts==200){
  				this.$notify({
	          title: '成功',
	          message: '保存成功',
	          type: 'success'
	        });
	        this.findAllSchoolInfo({id:3});
  			}else{
  				this.$notify({
	          title: '失败',
	          message: '保存失败',
	          type: 'error'
	        });
  			}
  		}).catch((error)=>{
				this.$notify({
          title: '失败',
          message: '保存失败',
          type: 'error'
        });
  		});
  	},
  	...mapActions(['findAllSchoolInfo','saveOneSchoolInfo']),
  }
}
</script>
<style scoped>
  .schoolInfo
  	table{
  		width: 100%;
  		border-collapse: collapse;	
  	}
  .schoolInfo
  	table tr td{
  		height: 25px;
  		border: 1px solid #ccc;
  	}
  .schoolInfo
  	table tr td:first-child{
  		width: 150px;
  		text-align: center;
  	}
  .schoolInfo
  	table tr td:last-child{
  		padding: 10px;
  	}
  .schoolInfo
  	button{
  		margin-top: 10px;
  		margin-right: 20px;
  		float: right;
  	}
  
</style>