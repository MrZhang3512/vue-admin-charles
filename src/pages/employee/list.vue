<template>
    <div>
        <!-- 按钮 -->
        <div>
        <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
        <el-button size="small" type="danger" @click="closeModalHande">删除</el-button>
        </div>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column label="编号" prop="id" fixed="left"></el-table-column>
            <el-table-column label="用户名" prop="username" fixed="left"></el-table-column>
            <el-table-column label="姓名" prop="realname" fixed="left"></el-table-column>
            <el-table-column label="性别" prop="gender"></el-table-column>
            <el-table-column label="联系方式" prop="telephone"></el-table-column>
            <el-table-column label="身份证号" prop="idCard" width="200"></el-table-column>
            <el-table-column label="银行卡号" prop="bankCard" width="200"></el-table-column>
            <el-table-column label="操作" fixed="right">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination
         layout="prev, pager, next"
        :total="50">
       </el-pagination>
        <!-- /分页 -->
        <!-- 模态框 -->
        <el-dialog
         :title="title"
         :visible.sync="visible"
         width="60%">
         <el-form label-width="80px" :model="form">
             <el-form-item label="用户名"><el-input v-model="form.username"></el-input></el-form-item>
             <el-form-item label="密码"><el-input type="password" v-model="form.password"></el-input></el-form-item>
             <el-form-item label="姓名"><el-input v-model="form.realname"></el-input></el-form-item>
             <el-form-item label="性别"><el-radio-group v-model="form.gender">
                     <el-radio label="男">男</el-radio>
                     <el-radio label="女">女</el-radio>r
                 </el-radio-group></el-form-item>
             <el-form-item label="手机号" ><el-input v-model="form.telephone"></el-input></el-form-item>
             <el-form-item label="身份证号"><el-input v-model="form.idCard"></el-input></el-form-item>
             <el-form-item label="银行卡号"><el-input v-model="form.bankCard"></el-input></el-form-item>
         </el-form>
         {{form}}
         <span slot="footer" class="dialog-footer">
         <el-button @click="closeModalHander" size="small">取 消</el-button>
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
        <!-- /模态框 -->
        </div>
</template>

<script>
import request from '@/utils/request'//自定义库
import querystring from 'querystring'//系统库
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        this.loadData();
    },
    methods:{
        submitHandler(){
            let url="http://localhost:6677/waiter/saveOrUpdate"
           request({
                 url,
                 method:"POST",
                 headers:{
                     "Content-Type":"application/x-www-form-urlencoded"
                 },
                 data:querystring.stringify(this.form)
             }).then((response)=>{
                 //请求结束
                 this.closeModalHander();
                 this.$message({
            type: 'success',
            message:response.message
          });
            this.loadData();
             })
    
        },
        loadData(){
            //this->vue的实例，通过this可以访问method的方法和data()数据
            let url="http://localhost:6677/waiter/findAll";
            //箭头函数中的this指向外部函数中的this，及vue的实例，而function中的this指向该函数中的this
            request.get(url).then((response)=>{this.employees=response.data})
        },
        toAddHandler(){
            this.title="录入员工信息";
            this.visible=true;
        },
        closeModalHande(){
            this.visible=false;
        },
        toDeleteHandler(id){
            //确认
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
        })
        },
        toUpdateHandler(row){
            this.title="修改员工信息";
               this.visible=true;
        },
        closeModalHander(){
            this.visible=false;
        }
    }
    
}
</script>

<style scoped>

</style>