<template>
	<el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="100px">
      <el-form-item label="用户名称" prop="personname">
        <el-input v-model="dataForm.personname" placeholder="用户名称, 如: 张安"></el-input>
      </el-form-item>
      <el-form-item label="手机号码" prop="mobile">
        <el-input v-model="dataForm.mobile" placeholder="手机号码"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="dataForm.password" placeholder="密码"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
	export default {
    data () {
	     return {
	        visible: false,
	        dataForm: {
	          	id: 0,
	          	personname: '',
	          	mobile: '',
	          	password: ''
	        },
	        dataRule: {
	          	personname: [
	            	{ required: true, message: '用户名不能为空', trigger: 'blur' }
	          	],
	          	mobile: [
	            	{ required: true, message: '手机号不能为空', trigger: 'blur' }
	          	],
	          	password: [
	            	{ required: true, message: '密码不能为空', trigger: 'blur' }
	          	]
	        }
	    }
    },
    methods: {
      	init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
          	if (this.dataForm.id) {
	            this.$http({
	              	url: this.$http.adornUrl(`/person/user/info/${this.dataForm.id}`),
	              	method: 'get',
	              	params: this.$http.adornParams()
	            }).then(({data}) => {
	              	if (data && data.code === 0) {
		                this.dataForm.personname = data.person.personname
		                this.dataForm.mobile = data.person.mobile
		                this.dataForm.password = data.person.password
	            	}
            	})
          	}
        })
    },
    // 表单提交
    dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
		        if (valid) {
		            this.$http({
		              	url: this.$http.adornUrl(`/person/user/${!this.dataForm.id ? 'save' : 'update'}`),
		              	method: 'post',
		              	data: this.$http.adornData({
		                	'id': this.dataForm.id || undefined,
		                	'personname':this.dataForm.personname,
		                	'mobile': this.dataForm.mobile,
		                	'password': this.dataForm.password
		              	})
		            }).then(({data}) => {
		              	if (data && data.code === 0) {
			                this.$message({
			                  	message: '操作成功',
			                  	type: 'success',
			                  	duration: 1500,
			                  	onClose: () => {
			                    	this.visible = false
			                    	this.$emit('refreshDataList')
			                  	}
			                })
		              	} else {
		                	this.$message.error(data.msg)
		              	}
		            })
		        }
	        })
      	}
    }
}
</script>

<style>
</style>