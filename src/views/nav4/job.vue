<template>
	<section>
	   <div class="toolbar">
		<el-form ref="form" :model="form" label-width="100px">
		  <el-form-item label="工单名称">
		    <el-input v-model="form.name"></el-input>
		  </el-form-item>
		  <el-form-item label="工单类型">
		    <el-select v-model="form.type" placeholder="工单类型" style="width:1130px">
		      <el-option label="web服务" value="web"></el-option>
		      <el-option label="mysql服务" value="mysql"></el-option>
		      <el-option label="设备申请" value="cmdb"></el-option>
		      <el-option label="权限申请" value="role"></el-option>
		      <el-option label="发布申请" value="deploy"></el-option>
		    </el-select>
		  </el-form-item>
		  <el-form-item label="工单内容">
		    <el-input type="textarea" v-model="form.comment" :rows="8"></el-input>
		  </el-form-item>
                  <el-form-item label="指派给">
                      <el-select v-model="form.userid" style="width:1130px">
			   <el-option v-for="(item,index) in option_data" :label="item.name" :key="item.id" :value="item.id">{{item.name}}</el-option>
                      </el-select> 
                  </el-form-item>
 
		  <el-form-item>
		    <el-button type="primary" @click="onSubmit">申请</el-button>
		  </el-form-item>
		</el-form> 
           </div>	
	</section>
</template>

<script>
  import { getUserListPage,addGongdan } from '../../api/api';
export default {
  data() {
     return {
	form: {
	  name: '',
	  type: '',
	  comment: '',
	  userid:'',
	},
        option_data: [],
    }
  },
methods: {
	getUsers() {
		getUserListPage().then((res) => {
			console.log(res)	
			this.option_data = res.data.users;
			//this.listLoading = false;
			//NProgress.done();
		});
	},
        onSubmit() {
		 console.log('submit!');
		 let para = Object.assign({}, this.form)	
			addGongdan(para).then((res) => {
				this.RoleLoading = true;
				//NProgress.done();
				this.$message({
					message: '添加成功，已发送审批邮件',
					type: 'success'
				});
			      this.$router.push({path: 'JobList'})	
			});
		},  
    }, 
    mounted() {
            this.getUsers();
	} 
  }
</script>
