<template>
    <!--  Form 组件提供了表单验证的功能，只需要通过 rules 属性传入约定的验证规则，  -->
    <!--  并将 Form-Item 的 prop 属性设置为需校验的字段名即可。  -->
    <el-form :model="ruleForm" :rules="rules" style="width: 33%" ref="ruleForm"
             label-position="right" label-width="100px" class="demo-ruleForm">

        <el-form-item label="id" prop="username">
          <el-input v-model="ruleForm.id" :disabled="true">{{ruleForm.id}}</el-input>
        </el-form-item>

        <el-form-item label="账户名" prop="username">
          <el-input v-model="ruleForm.username" :disabled="true">{{ruleForm.username}}</el-input>
        </el-form-item>

        <el-form-item label="姓名" prop="nickname">
          <el-input v-model="ruleForm.nickname">{{ruleForm.nickname}}</el-input>
        </el-form-item>
        <el-form-item label="电话" prop="telephone">
          <el-input v-model="ruleForm.telephone">{{ruleForm.telephone}}</el-input>
        </el-form-item>
        <el-form-item label="性别">
          <el-radio-group v-model="ruleForm.gender" size="small">
            <el-radio :label="1">男</el-radio>
            <el-radio :label="2">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="地址" prop="address">
          <el-input placeholder="请输入地址" v-model="ruleForm.address"></el-input>
        </el-form-item>
        <el-form-item>

            <el-button type="success" @click="submitForm('ruleForm')" icon="el-icon-check">确认保存</el-button>
            <el-button type="warning" @click="resetForm()" icon="el-icon-close">取消保存</el-button>

        </el-form-item>
    </el-form>
</template>
<script>
    export default {
        //  初始化函数
        created() {
            const _this=this;
            axios.get('http://localhost:8030/client/Userclient/findById/'+this.$route.query.id)
                .then(function (res){
                    console.log(res)
                    _this.ruleForm = res.data;
                })
        },
        data() {
          return {
            user: {
              id: '',
              name: '',
            },
            ruleForm: {
              id: '',
              username: '',
              nickname: '',
              gender: 1,
              telephone: '',
              address: '',
            },
            rules: {
              nickname: [
                {required: true, message: '请输入姓名', trigger: 'blur'},
                {min: 2, max: 20, message: '长度在 2 到 20 个字符', trigger: 'blur'}
              ],telephone: [
                {required: true, message: '请输入电话号码', trigger: 'blur'},
                {min: 11, max: 11, message: '长度为  11 个字符', trigger: 'blur'}
              ],address: [
                {required: true, message: '请输入地址', trigger: 'blur'},
                {min: 2, max: 20, message: '长度在 2 到 20 个字符', trigger: 'blur'}
              ],
            }
          };
        },
        methods: {
            //  确认保存按钮绑定事件
            submitForm(formName) {
                const _this=this
              if(_this.ruleForm.gender==1){
                _this.ruleForm.gender='男';
              }else {
                _this.ruleForm.gender='女';
              }
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        _this.$options.methods.updateData(_this);
                    }
                })
            },
            // 保存修改数据函数
            updateData(_this){
                axios.post('http://localhost:8030/client/Userclient/update', _this.ruleForm)
                    .then(function (res) {
                        if(res.data){
                            _this.$message({
                                showClose: true,
                                center: true,
                                type: 'success',
                                message:'账户《'+_this.ruleForm.username+'》:修改成功',
                            });
                            //  如果修改成功则跳转到对应路由
                            _this.$router.push('/Usermanage')
                        }else{
                            _this.$alert('账户《'+_this.ruleForm.name+'》信息修改失败,详情信息请联系技术人员!', 'error:', {
                                confirmButtonText: '确定',
                                callback: action => {
                                    _this.$message({
                                        center: true,
                                        type: 'error',
                                        message: '账户《'+_this.ruleForm.name+'》:信息修改失败',
                                    });
                                }
                            });
                        }
                    });
            },
            //  取消保存按钮绑定事件
            resetForm() {
                const _this=this
                //  危险按钮再次确认功能
                this.$confirm('确认取消修改吗?当前资料修改尚未保存,返回则所有修改将不会生效', '提示:', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    //  如果取消修改则跳转到/Usermanage
                    _this.$router.push('/Usermanage')
                    this.$message({
                        showClose: true,
                        center: true,
                        type: 'success',
                        message: '已取消修改!',
                    });
                }).catch(() => {
                    this.$message({
                        showClose: true,
                        center: true,
                        type: 'info',
                        message: '点击"确认保存"按钮可保存生效'
                    });
                });
            }
        },
    }
</script>