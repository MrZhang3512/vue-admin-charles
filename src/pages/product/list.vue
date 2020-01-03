<template>
<div>
    <h2>产品管理</h2>
    <!-- 按钮 -->
    <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
    <el-button size="small" type="danger">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="products">
        <el-table-column label="编号" prop="id"></el-table-column>
        <el-table-column label="产品名称" prop="name"></el-table-column>
        <el-table-column label="价格" prop="price"></el-table-column>
        <el-table-column label="描述" prop="description"></el-table-column>
        <el-table-column label="所属产品" prop="categoryId"></el-table-column>
        <el-table-column label="操作" >
             <template v-slot="slot">
                    <a href="" @click.prevent="todeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toupdateHandler">修改</a>
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
    <!-- 模式框 -->
     <!--模式框-->
       <el-dialog
         title="录入产品信息"
         :visible.sync="visible"
         width="60%">
         <el-form :model="form" label-width="80px">
             <!-- <el-form-item label="编号">
                 <el-input v-model="form.id"></el-input>
             </el-form-item> -->
             <el-form-item label="产品名称">
                 <el-input v-model="form.name"></el-input>
             </el-form-item>
             <el-form-item label="价格">
                 <el-input v-model="form.price"></el-input>
             </el-form-item>
             <el-form-item label="描述">
                 <el-input v-model="form.description"></el-input>
             </el-form-item>
             <el-form-item label="所属栏目">
                  <template>
  <el-select v-model="form.status" placeholder="请选择">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.name"
      :value="item.parentId">
    </el-option>
  </el-select>
</template>
             </el-form-item>
         </el-form>
         {{form}}
         <span slot="footer" class="dialog-footer">
         <el-button @click="closeModalHander" size="small">取 消</el-button>
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
    <!-- /模式框 -->
</div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            products:[],
            visible:false,
            form:{},
            options: []
        }
    },
    created(){
        this.loadData();
    },
    methods:{
        loadData(){
            let url="http://localhost:6677/product/findAll";
            request.get(url).then((response)=>{
                this.products=response.data;
            });
            let url1="http://localhost:6677/category/findAll";
            request.get(url1).then((response)=>{
                this.options=response.data;
            });
        },
        closeModalHander(){
            this.visible=false;
        },
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate";
            request({
                url,
                method:"POST",
                 headers:{
                     "Content-Type":"application/x-www-form-urlencoded"
                 },
                 data:querystring.stringify(this.form)
            }).then((response)=>{   
                this.closeModalHander();
                 this.$message({
                type: 'success',
                message:response.message
          });
            this.loadData();})
        },
        toAddHandler(){
            this.visible=true;
        },
        todeleteHandler(id){
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
        }

    }
}
</script>
<style scoped>

</style>