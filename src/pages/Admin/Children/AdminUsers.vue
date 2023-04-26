<!--
 * @Author: Ender-Zhang YUZ302@pitt.edu
 * @Date: 2023-04-23 14:46:21
 * @LastEditors: Ender-Zhang YUZ302@pitt.edu
 * @LastEditTime: 2023-04-23 16:47:03
 * @FilePath: \web-client\src\pages\Admin\Children\AdminUsers.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div id="admin-users">
    <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="id"
        label="User ID">
      </el-table-column>
      <el-table-column
        prop="user_name"
        label="Account Number">
      </el-table-column>
      <el-table-column
        prop="user_phone"
        label="Cell phone">
      </el-table-column>
      <el-table-column
        prop="user_nickname"
        label="Nickname">
      </el-table-column>
      <el-table-column
        prop="user_address"
        label="Address">
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
  import {getAllUsers} from '../../../api/index'
  export default {
    data() {
      return {
        tableData: []
      }
    },
    mounted(){
      this.getUsers();
    },
    methods: {
      async getUsers(){
        const results = await getAllUsers();
        if(results.success_code === 200){
          this.tableData = results.message;
        }
      }
    }
  }
</script>

<style scoped>

</style>
