<template>
   <section>
      <div class="toolbar"> 

        <el-row :gutter="21">
            <el-col :span="8">
                <div id="chartColumn" style="width:100%; height:400px;">
                <el-tree
  		:data="data"
		node-key="id"
  		:props="defaultProps"
  		accordion
  		@node-click="handleNodeClick">
		</el-tree>
           </div>
            </el-col>
            <el-col :span="16">
	<div>
	<el-form ref="search_form" :model="search_form" label-width="80px">
	<el-select v-model="search_form.value" multiple filterable placeholder="请选择" style="width:80%">	
    	<el-option
      		v-for="item in options"
      		:key="item.templateid"
      		:label="item.name"
      		:value="item.templateid">
    	</el-option>
  	</el-select>      
    <el-button type="primary" @click.native="addSubmit" :loading="addLoading">绑定</el-button>
	</el-form>
	</div>
        <br/>
            </el-col>
            <el-col :span="12">
               <el-table
    		ref="multipleTable"
    		:data="tableData"
    		tooltip-effect="dark"
		@selection-change="selsChange"
    		style="width: 100%">
    		<el-table-column
      		type="selection"
      		width="55" 
		>
    		</el-table-column>
		   <el-table-column
      			prop="ip"
      			label="主机"
      			width="140">
    		  </el-table-column>
    		  <el-table-column
      			prop="template"
      			label="模板"
      			show-overflow-tooltip>
    		</el-table-column>		   
 
  		</el-table>
            </el-col>
            <el-col :span="12">
                <div id="chartPie" style="width:100%; height:400px;"></div>
            </el-col> 
        </el-row>
     </div>
   </section>
</template>

<script>
	import util from '../../common/js/util'
	import {  getTree, getTemplate,getProduct_Ip,addHost_temlpate } from '../../api/api';

	export default {
		data() {
			return {
				data: [],
				send_data: '',
				defaultProps: {
          			children: 'children',
          			label: 'label'
        			},
				input: '',
      				select: '',
				options: [],
				sels: [],
				ips: [],
				addLoading: false,
				search_form: {
					value: '',	
					},
				tableData:[]
			   }
		},
		methods: {
			getTree() {	
				getTree().then((res) => {
					this.data = res.data;
				});
			},
			getTemplate () {
                                getTemplate().then((res) => {	
                                        this.options = res.data

                                })
                          },
			 handleNodeClick(data) {
			    this.send_data = data 
			    getProduct_Ip(data).then((res) => {
					console.log(res.data)
					this.tableData=res.data	
				}); 
      			}, 
			 selsChange: function (sels) {	
                                this.sels = sels; 
                        },
			addSubmit: function () {
                                this.$refs.search_form.validate((valid) => {
                                        if (valid) {
                                                this.$confirm('确认提交吗？', '提示', {}).then(() => {
                                                        this.addLoading = true;
                                                        //NProgress.start();
							var ids = this.sels.map(item => item.id).toString();
                                                        let para = Object.assign({}, this.search_form); 
                                                        addHost_temlpate(ids,para).then((res) => {
                                                                this.addLoading = false;
                                                                //NProgress.done();
                                                                this.$message({
                                                                        message: '提交成功',
                                                                        type: 'success'
                                                                });
                                                                this.getTree();
                        					this.getTemplate();	
								getProduct_Ip(this.send_data).then((res) => {
                                        				this.tableData=res.data
                                				});
                                                        });
                                                });
                                        }
                                });
                        },

		   },
			//删除
		mounted() {
			this.getTree();
			this.getTemplate();
		}
	}

</script>

