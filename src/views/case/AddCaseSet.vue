<template>
  <el-dialog title="新增用例集" :visible.sync="$store.state.addcasesetshow" style="height: 100%;" :close-on-click-modal="false"
             @close="closeAddCaseSetView">
    <el-form :model="form" :rules="rules" ref="form">
      <el-form-item label="名称" :label-width="formLabelWidth" prop="setName">
        <el-input placeholder="请输入用例集名称" v-model="form.setName" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="步骤" :label-width="formLabelWidth" prop="setDesc">
        <el-input type="textarea" v-model="form.setDesc" placeholder="请在此填写描述" :rows="15"></el-input>
      </el-form-item>
      <el-form-item label="所属项目" :label-width="formLabelWidth" prop="projectId" style="text-align: left">
        <el-select v-model="form.projectId" filterable placeholder="请选择项目">
          <el-option
            v-for="item in projects"
            :key="item.pid"
            :label="item.pname"
            :value="item.pid">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="状态" :label-width="formLabelWidth" prop="caseSetStatus" style="text-align: left">
        <el-select v-model="form.setStatus" placeholder="请选择状态">
          <el-option label="待完成" value="0"></el-option>
          <el-option label="待维护" value="1"></el-option>
          <el-option label="已完成" value="2"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="启用" :label-width="formLabelWidth" style="text-align: left">
        <el-switch
          v-model="form.valid"
          active-color="#13ce66"
          inactive-color="#ff4949">
        </el-switch>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button type="primary" @click="addCaseSet">确 定</el-button>
      <el-button @click="closeAddCaseSetView">取 消</el-button>
    </div>
  </el-dialog>
</template>

<script>


  export default {
    data() {
      return {
          projects:[
          ],
        rules:{
            projectId:[
                {required:true,message:'请选择项目',trigger:'change'},
            ],
            setName:[
            {required:true,message:'请输入用例集名称',trigger:'blur'},
          ],

            setDesc:[
            {required:true,message:'请输入用例集步骤描述',trigger:'change'},
          ]

        },
        dialogTableVisible: false,
        dialogFormVisible: false,
        form: {
            projectId:'',
            setName:'',
            setDesc:'',
            setStatus:'0',
            valid:true,
        },
        formLabelWidth: '80px',
      };
    },
    methods:{
      addCaseSet(){
        const caseSet = {
            projectId:this.form.projectId,
            setName:this.form.setName,
            setDesc:this.form.setDesc,
            setStatus:this.form.setStatus,
            valid: this.form.valid?1:0,
        };
        this.$refs['form'].validate(bol=>{
          if (bol){
            this.$axios.post('/apis/testcaseset/',caseSet).then(res=>{
              this.$notify({
                title: '成功',
                type:'success',
                message:res.data.msg
              });
              this.closeAddCaseSetView();
              this.$emit('getCaseSet');
            }).catch(err=>{
              this.$notify({
                title: '失败',
                type:'error',
                message:err
              });
            });

          }
        });
      },
      closeAddCaseSetView(){
        this.$refs['form'].resetFields();
        this.$store.commit('changeAddCaseSetShow',false);
      },
      getProjects(){
        this.$axios.get('/apis/project/list').then(res=> {
            this.projects = res.data.data;
        }).catch(err=>{
          this.$notify({
            title: '失败',
            type:'error',
            message:err
          });
        });
      }
    },
    computed: {

    },
    components:{
    },
      created() {
        this.getProjects();
      }
  };
</script>
