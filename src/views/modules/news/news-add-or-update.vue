<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="newstitle">
      <el-input v-model="dataForm.newstitle" placeholder="标题"></el-input>
    </el-form-item>
    <el-form-item label="容内" prop="newscontent">
      <el-input v-model="dataForm.newscontent" placeholder="容内"></el-input>
    </el-form-item>
    <el-form-item label="布人发" prop="newsissuer">
      <el-input v-model="dataForm.newsissuer" placeholder="布人发"></el-input>
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
          newstitle: '',
          newscontent: '',
          newsissuer: '',
          createTime: ''
        },
        dataRule: {
          newstitle: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          newscontent: [
            { required: true, message: '容内不能为空', trigger: 'blur' }
          ],
          newsissuer: [
            { required: true, message: '布人发不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '发布时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/news/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.newstitle = data.news.newstitle
                this.dataForm.newscontent = data.news.newscontent
                this.dataForm.newsissuer = data.news.newsissuer
                this.dataForm.createTime = data.news.createTime
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
              url: this.$http.adornUrl(`/news/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'newstitle': this.dataForm.newstitle,
                'newscontent': this.dataForm.newscontent,
                'newsissuer': this.dataForm.newsissuer,
                'createTime': this.dataForm.createTime
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
