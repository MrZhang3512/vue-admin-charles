<template>
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="customers">
            <el-table-column label="编号" prop="id"> </el-table-column>
            <el-table-column label="用户名" prop="username"> </el-table-column>
            <el-table-column label="真实姓名" prop="realname"> </el-table-column>
            <el-table-column label="联系方式" prop="telephone"> </el-table-column>
            <el-table-column label="操作"> 
                <template v-slot="slot">
                    <a href="" @click.prevent="todeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toupdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!--/表格-->
        <!--分页-->
         <el-pagination
         layout="prev, pager, next"
        :total="50">
       </el-pagination>
       <!--/分页-->
       <!--模式框-->
       <el-dialog
         title="录入顾客信息"
         :visible.sync="visible"
         width="60%">
         <el-form :model="form" label-width="80px">
             <el-form-item label="用户名">
                 <el-input v-model="form.username"></el-input>
             </el-form-item>
             <el-form-item label="密码">
                 <el-input v-model="form.password" type="password"></el-input>
             </el-form-item>
             <el-form-item label="真实姓名">
                 <el-input v-model="form.realname"></el-input>
             </el-form-item>
             <el-form-item label="手机号码">
                 <el-input v-model="form.telephone"></el-input>
             </el-form-item>
         </el-form>
           ---{{form}}
         <span slot="footer" class="dialog-footer">
         <el-button @click="closeModalHander" size="small">取 消</el-button>
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
        <!--/模式框-->
        </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
import { type } from 'os'
export default {
    //用于存放网页中用于调用的方法
    methods:{
        loadData(){
            let url="http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            this.customers=response.data;
        })
        },
        submitHandler(){
    
            //通过request与后台进行交互，并且携带参数
             let url="http://localhost:6677/customer/saveOrUpdate"
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
        todeleteHandler(id){
            //确认
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/customer/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新sju 
                 this.$message({
                type: 'success',
                message: '删除成功!'+id
            });
            this.loadData();
            });
           
            })
            },
        toupdateHandler(row){
            this.form=row;
            this.visible=true;
        },
        toAddHandler(){
            this.form={
                type:"customer"
            };
            this.visible=true;
        },
        closeModalHander(){
            this.visible=false;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            customers:[],
            form:{}

        }
    },
    created(){
        //this为当前vue实例对象
        //vue实例创建完毕
        this.loadData();
        
    }
}
</script>

<style scoped>

</style>