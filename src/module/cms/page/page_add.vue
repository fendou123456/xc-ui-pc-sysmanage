<template>
  <div>
    <el-form   :model="pageForm" label-width="80px" :rules="pageFormRules" ref="pageForm" >
      <el-form-item label="所属站点" prop="siteId">
        <el-select v-model="pageForm.siteId" placeholder="请选择站点">
          <el-option
            v-for="item in siteList"
            :key="item.siteId"
            :label="item.siteName"
            :value="item.siteId">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="选择模版" prop="templateId">
        <el-select v-model="pageForm.templateId" placeholder="请选择">
          <el-option
            v-for="item in templateList"
            :key="item.templateId"
            :label="item.templateName"
            :value="item.templateId">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="页面名称" prop="pageName">
        <el-input v-model="pageForm.pageName" auto-complete="off" ></el-input>
      </el-form-item>

      <el-form-item label="别名" prop="pageAliase">
        <el-input v-model="pageForm.pageAliase" auto-complete="off" ></el-input>
      </el-form-item>
      <el-form-item label="访问路径" prop="pageWebPath">
        <el-input v-model="pageForm.pageWebPath" auto-complete="off" ></el-input>
      </el-form-item>

      <el-form-item label="物理路径" prop="pagePhysicalPath">
        <el-input v-model="pageForm.pagePhysicalPath" auto-complete="off" ></el-input>
      </el-form-item>
      <el-form-item label="数据Url" prop="dataUrl">
        <el-input v-model="pageForm.dataUrl" auto-complete="off" ></el-input>
      </el-form-item>

      <el-form-item label="类型">
        <el-radio-group v-model="pageForm.pageType">
          <el-radio class="radio" label="0">静态</el-radio>
          <el-radio class="radio" label="1">动态</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="创建时间">
        <el-date-picker type="datetime" placeholder="创建时间" v-model="pageForm.pageCreateTime"></el-date-picker>
      </el-form-item>

    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="go_back">返回</el-button>
      <el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
    </div>
  </div>
</template>
<script>
  /*编写页面静态部分，即model及vm部分*/
  import * as cmsApi from '../api/cms'
import { type } from 'os';
  export default {
    data(){
      return {
        //新增界面数据
        pageForm: {
          siteId:'',
          templateId:'',
          pageName: '',
          pageAliase: '',
          pageWebPath: '',
          dataUrl:'',
          pageParameter:'',
          pagePhysicalPath:'',
          pageType:'',
          pageCreateTime: new Date()
        },
        //配置表单校验规则
        pageFormRules: {
          siteId:[
            {required: true, message: '请选择站点', trigger: 'blur'}
          ],
          templateId:[
            {required: true, message: '请选择模版', trigger: 'blur'}
          ],
          pageName: [
            {required: true, message: '请输入页面名称', trigger: 'blur'}
          ],
          pageWebPath: [
            {required: true, message: '请输入访问路径', trigger: 'blur'}
          ],
          pagePhysicalPath: [
            {required: true, message: '请输入物理路径', trigger: 'blur'}
          ],
          dataUrl: [
            {required: true, message: '请输入数据url', trigger: 'blur'}
          ]
        },
        //站点列表
        siteList:[],
        //模板列表
        templateList:[],
        //加载效果标记
        addLoading: false,
      }
    },

    methods:{
      //提交
      addSubmit(){
        this.$refs['pageForm'].validate((valid) => {
          if(valid){    //表单校验成功
            this.$confirm('你确认提交吗?', '提示', {}).then(() => {
              this.addLoading = true ;
              //调用page_add方法请求服务端相关接口
              cmsApi.page_add(this.pageForm).then((res) => {
                //解析服务端的响应内容
                if(res.success){
                  // this.$message({
                  //   message: '提交成功',
                  //   type: 'success'
                  // })
                  this.$message.success('提交成功') ;
                  //将表单清空
                  this.$refs['pageForm'].resetFields() ;
                }else{
                  this.$message.error('提交失败');
                }
              })
            });
          }
        })
      },
      //返回
      go_back(){
        //取到当前路由并设置新路径
        this.$router.push({
          path:'/cms/page/list',
          query:{
            //取出路由中的参数
            page:this.$route.query.page,
            siteId:this.$route.query.siteId
          }
        })
      },

      //查询站点列表
      query_siteList(){
        cmsApi.site_list().then((res) => {
          this.siteList = res.queryResult.list
        })
      },
      //查询模板列表
      query_templateList(){
        cmsApi.temp_list().then((res) => {
          this.templateList = res.queryResult.list
        })
      }
    },

    created() {

    },

    mounted() {
      //获取站点列表
      this.query_siteList()
      //获取模板列表
      this.query_templateList()

    }
  }
</script>
<style>
  /*编写页面样式*/

</style>
