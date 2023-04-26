<template>
  <div>
    <el-table
      :data="tempData"
      style="width: 100%">
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="Product Name">
              <span>{{ props.row.short_name }}</span>
            </el-form-item>
            <el-form-item label="product ID">
              <span>{{ props.row.goods_id }}</span>
            </el-form-item>
            <el-form-item label="Product Categories">
              <span>{{ category[props.row.category - 1] }}</span>
            </el-form-item>
            <el-form-item label="Product Price">
              <span>{{ (props.row.price /100) | priceFormat}}</span>
            </el-form-item>
            <el-form-item label="Merchandise Inventory">
              <span>{{ props.row.counts }}</span>
            </el-form-item>
            <el-form-item label="Product Description">
              <span>{{ props.row.goods_name }}</span>
            </el-form-item>
            <el-form-item label="Product Images">
              <img :src="props.row.thumb_url" style="width:70px"/>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column
        label="product ID"
        prop="goods_id">
      </el-table-column>
      <el-table-column
        label="Product Name"
        prop="short_name">
      </el-table-column>
      <el-table-column
        label="Description"
        prop="goods_name">
      </el-table-column>
      <el-table-column label="Operation">
        <template slot-scope="props">
          <el-button
            size="mini"
            @click="handleEdit(props.$index, props.row)">Editor</el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(props.$index, props.row)">Delete</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      background
      layout="prev, pager, next"
      :page-count="pageNum"
      @current-change="handleCurrentChange">
    </el-pagination>
  </div>
</template>

<script>
  import {getAllgoods,deleteRecomGoods} from './../../../api/index';
  import {mapState} from 'vuex';
  import {mapActions} from 'vuex'

  export default {
    data() {
      return {
        category: ['Beauty Whitening','Health Care','Medical Books','Medical Devices','Vision Protection'],
        currentIndex: 1,
        pageSize: 5,
        tableData: [],
        tempData: [],
      }
    },
    mounted(){
      this.getAllGoods();
    },
    methods: {
      handleEdit(index, row) {
        console.log(index, row);
        window.localStorage.setItem('goodsInfo',JSON.stringify(row));
        this.$router.replace('/admin/adminupdate');
      },
      async handleDelete(index, row) {
        this.$confirm('Are you sure you want to delete this product permanently?', 'Tips', {
          confirmButtonText: 'Determine',
          cancelButtonText: 'Cancellation',
          type: 'warning'
        }).then( async() => {
		  let result = await deleteRecomGoods(row.goods_id);
          if(result.success_code === 200){
            this.$message({
              type: 'success',
              message: 'Deleted'
            });
          }
        }).catch(() => {
          this.$message({
            type: 'info',
            message: 'Deleted'
          });
        });
      },
      handleCurrentChange(val) {
        this.currentIndex = val;
        this.tempData = [];
        this.tableData.forEach((data,index)=>{
          if(index >= (this.currentIndex-1) * this.pageSize && index < (this.currentIndex) * this.pageSize){
            this.tempData.push(data);
          }
        });
      },
      async getAllGoods(){
        let result = await getAllgoods();
        if(result.success_code === 200){
          this.tableData = result.message;
          console.log(this.tableData);
          this.tableData.forEach((data,index)=>{
            if(index >= (this.currentIndex-1) * this.pageSize && index < (this.currentIndex) * this.pageSize){
              this.tempData.push(data);
            }
          });
        }
      },
    },
    computed: {
      pageNum(){
        return Math.ceil(this.tableData.length / this.pageSize);
      }
    },
    filters: {
      priceFormat(price) {
        return price.toFixed(2);
      },
    },
  }
</script>

<style scoped>
  .el-table{
    margin: 20px auto 50px;
  }
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 90px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
  .el-pagination{
    text-align: center;
  }
</style>

