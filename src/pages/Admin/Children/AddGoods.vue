<template>
  <div id="addgoods">
    <h3>Product Listing</h3>
    <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
      <el-form-item label="Product ID" prop="goods_id" style="width:250px">
        <el-input v-model="ruleForm.goods_id" type="number"></el-input>
      </el-form-item>
      <el-form-item label="Product Name" prop="short_name" style="width:500px">
        <el-input v-model="ruleForm.short_name"></el-input>
      </el-form-item>
	  <el-form-item label="Product Images" style="width:500px">
        <el-upload
		  list-type="picture-card"
		  :on-preview="handlePictureCardPreview"
		  :on-change="handleChange"
		  :auto-upload="false"
		  :multiple="false"
		  limit:1
		  action="https://team-rolls-royce-backend.glitch.me"
		>
		  <i class="el-icon-plus"></i>
		</el-upload>
		<el-dialog :visible.sync="dialogVisible">
		  <img width="100%" :src="dialogImageUrl" alt="">
		</el-dialog>
      </el-form-item>
      <el-form-item label="Product Specials" prop="price" style="width:250px">
        <el-input v-model="ruleForm.price" type="number"></el-input>
      </el-form-item>
      <el-form-item label="Merchandise Inventory" prop="counts" style="width:250px">
        <el-input v-model="ruleForm.counts" type="number"></el-input>
      </el-form-item>
      <el-form-item label="Product Categories" prop="category">
        <el-select v-model="ruleForm.category" placeholder="Please select">
          <el-option
           v-for="item in options"
          :key="item.value"
          :label="item.label"
          :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="Product Tags" prop="sales_tip" style="width:500px">
        <el-input v-model="ruleForm.sales_tip"></el-input>
      </el-form-item>
      <el-form-item label="Product Details" prop="goods_name" style="width:500px">
        <el-input type="textarea" v-model="ruleForm.goods_name" autosize></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">Create Now</el-button>
        <el-button @click="resetForm('ruleForm')">Reset</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
  import {mapState} from 'vuex';
  import {addGoodsToRecom, getAllgoods} from './../../../api/index';

  export default {
    data() {
      return {
	    fileList: [],
		dialogImageUrl: '',
		dialogVisible: false,
        ruleForm: {
          goods_id: null,
          short_name: '',
          goods_name: '',
          price: null,
          sales_tip: '',
          counts: null,
          category: 1,
        },
        rules: {
          goods_id: [
            { required: true, message: 'Please enter the product ID', trigger: 'blur' }
          ],
          short_name: [
            { required: true, message: 'Please enter the product name', trigger: 'blur' }
          ],
          goods_name: [
            { required: true, message: 'Please fill in the product details', trigger: 'blur' }
          ],
          price: [
            { required: true, message: 'Please enter the product special price', trigger: 'blur' }
          ],
          counts: [
            { required: true, message: 'Please enter product inventory', trigger: 'blur' }
          ],
          category: [
            { required: true, message: 'Please select a product category number', trigger: 'blur' }
          ],
          sales_tip: [
            { required: true, message: 'Please enter the product label', trigger: 'blur' }
          ],
        },
        options: [{
          value: 1,
          label: 'Beauty Whitening'
        }, {
          value: 2,
          label: 'Health Care'
        },{
          value: 3,
          label: 'Medical Books'
        },{
          value: 4,
          label: 'Medical Devices'
        },{
          value: 5,
          label: 'Vision Protection'
        }],
      };
    },
    methods: {
	  handleChange(file, fileList) {
	    if(fileList.length > 1){
			fileList = fileList.slice(-1)
		}
        this.fileList = fileList;
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
      async submitForm(formName) {
        console.log(formName);
        let flag = false;
        this.$refs[formName].validate((valid) => {
          if (valid) {
            console.log("valid", valid);
            this.$refs[formName].model.goods_id = Number(this.$refs[formName].model.goods_id);
            this.$refs[formName].model.counts = Number(this.$refs[formName].model.counts);
            this.$refs[formName].model.price = Number(this.$refs[formName].model.price);
            console.log(this.$refs[formName].model);
            flag = true;
            console.log("flag", flag);
            // console.log("fileList", this.fileList);
          }
        });
        // if(!this.fileList.length){
        //   flag = false;
        // }
        console.log("flag", flag);
        if(flag){
          let formData = new FormData();
          formData.append('goods_id', this.$refs[formName].model.goods_id);
          formData.append('short_name', this.$refs[formName].model.short_name);
          formData.append('goods_name', this.$refs[formName].model.goods_name);
          formData.append('counts', this.$refs[formName].model.counts);
          formData.append('price', this.$refs[formName].model.price);
          formData.append('sales_tip', this.$refs[formName].model.sales_tip);
          formData.append('category', this.$refs[formName].model.category);
          // formData.append('goods_img', this.fileList[0].raw);
          console.log("formData", formData);
          let result = await addGoodsToRecom(formData);

          /*
              let result = await addGoodsToRecom({
                goods_id: this.$refs[formName].model.goods_id,
                short_name: this.$refs[formName].model.short_name,
                goods_name: this.$refs[formName].model.goods_name,
                counts: this.$refs[formName].model.counts,
                price: this.$refs[formName].model.price,
                sales_tip: this.$refs[formName].model.sales_tip,
                category: this.$refs[formName].model.category
              });
          */
          if(result.success_code === 200){
            this.$message({
              type: 'success',
              message: 'Added successfully'
            });
            this.$router.replace('/admin');
            getAllgoods();
          }else if(result.success_code === 500){
            this.$message({
              type: 'info',
              message: 'Product already exists'
            });
          }else{
            this.$message({
              type: 'error',
              message: 'Failed to add'
            });
          }
        }
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      }
    }
  }
</script>

<style scoped>
#addgoods{
  padding-top: 20px;
}
#addgoods>h3{
  font-weight: bolder;
  margin: 10px 10px 30px 10px;
}
</style>

