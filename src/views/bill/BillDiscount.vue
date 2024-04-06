<!--
 * @Author: 俊宏 oncwnuLAeJWzo2Rk1De_oNwKmI9s@git.weixin.qq.com
 * @Date: 2024-03-30 16:50:49
 * @LastEditors: 俊宏 oncwnuLAeJWzo2Rk1De_oNwKmI9s@git.weixin.qq.com
 * @LastEditTime: 2024-04-01 22:28:53
 * @FilePath: \CinemaManagerAdminVue\src\views\bill\BillDiscount.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div class="bill-discount">
    <!--导航区域-->
    <div class="board">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>优惠管理</el-breadcrumb-item>
        <el-breadcrumb-item>优惠信息设置</el-breadcrumb-item>
      </el-breadcrumb>
    </div>

    <el-card class="box-card">
      <el-row :gutter="20">
        <el-col :span="6">
        </el-col>
      </el-row>
      <br>
      <el-row>
        <el-col :span="24">
          <el-button type="primary" @click="addDialogVisible = true" style="font-size: 18px;">
             添加优惠活动
          </el-button>
        </el-col>
      </el-row>
    </el-card>

    <!--活动列表-->
    <el-table :data="disList" style="width: 100%" border stripe >
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column prop="disId" label="活动编号" width="100"></el-table-column>
        <el-table-column prop="disName" label="活动名称"></el-table-column>
        <el-table-column prop="disOff" label="活动折扣"></el-table-column>
        <el-table-column prop="disState" label="活动状态">
          <template slot-scope="scope">
            {{ scope.row.disState === 1 ? '活动进行' : '活动暂停' }}
          </template>
        </el-table-column>

        <el-table-column label="操作" width="360" fixed="right">
          <template slot-scope="scope">
            <el-tooltip effect="dark" content="修改活动信息" placement="top" :enterable="false" :open-delay="500">
              <el-button type="primary" @click="showEditDialog(scope.row)"  style="font-size: 18px;">
                 编辑优惠
              </el-button>
            </el-tooltip>
            <el-tooltip effect="dark" content="删除活动" placement="top" :enterable="false" :open-delay="500">
              <el-button type="danger"  style="font-size: 18px;"
                         @click="isAbleDelete(scope.row.disId)">
                          删除优惠
              </el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>


      <!--添加活动对话框-->
    <el-dialog title="添加优惠活动"  width="60%" :visible.sync="addDialogVisible" @close="addDialogClosed" >
      <!--内容主题区-->
      <el-form :model="addInfo"  ref="addInfoRef" label-width="100px">
        <el-form-item label="活动编号" prop="disid">
          <el-input v-model="addInfo.disId" ></el-input>
        </el-form-item>
        <el-form-item label="活动名称" prop="disName">
          <el-input v-model="addInfo.disName"></el-input>
        </el-form-item>
        <el-form-item label="活动折扣" prop="disOff">
          <el-input v-model="addInfo.disOff"></el-input>
        </el-form-item>
        <el-form-item label="活动状态" prop="disState">
          <el-input v-model="addInfo.disState"></el-input>
        </el-form-item>
      </el-form>
      <!--底部区域-->
      <span slot="footer" class="dialog-footer">
      <el-button  @click="addDialogVisible = false" style="font-size: 18px;">  取 消</el-button>
      <el-button type="primary" @click="saveDiscounts" style="font-size: 18px;">  确 定</el-button>
      </span>
    </el-dialog>

    <!--修改活动对话框-->
    <el-dialog title="修改优惠活动"  width="60%" :visible.sync="editDialogVisible">
      <el-form :model="editInfo"  ref="editInfoRef" label-width="100px">
        <el-form-item label="优惠活动编号" prop="disId">
          <el-input v-model="editInfo.disId" disabled></el-input>
        </el-form-item>
        <el-form-item label="活动名称" prop="disName">
          <el-input v-model="editInfo.disName"></el-input>
        </el-form-item>
        <el-form-item label="活动折扣" prop="disOff">
          <el-input v-model="editInfo.disOff"></el-input>
        </el-form-item>
        <el-form-item label="活动状态" prop="disState">
          <el-input v-model="editInfo.disState"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false" style="font-size: 18px;">  取 消</el-button>
        <el-button type="primary" @click="editDisInfo" style="font-size: 18px;">  确 定</el-button>
      </span>
    </el-dialog>

  </div>

</template>

<script>
import global from "@/assets/css/global.css"
import moment from 'moment'
export default {
  name: "BillDiscount",
  data() {
    return {
      disInfo: {
        disId: '',
        disName:'',
        disOff: '',
        disState: '',
      },
      disList: [],
      addInfo: {
        disId: '',
        disName:'',
        disOff: '',
        disState: '',
      },
      //控制对话框的显示与隐藏
      addDialogVisible: false,
      editInfo: {
        disId: '',
        disName:'',
        disOff: '',
        disState: '',
      },
      //控制修改对话框的显示与隐藏
      editDialogVisible: false,
    }
  },
  created() {
    this.getDiscount()
    this.getDisList()
  },
  methods: {
    async getDisList() {
      this.disInfo.disName = this.inputHallName
      this.disInfo.hallCategory = this.selectedHallCategory
      const _this = this;
      axios.get('discount/', {params: _this.disInfo}).then(resp => {
        console.log(resp);
        _this.disList = resp.data.data;
        _this.total = resp.data.total;
        _this.disInfo.pageSize = resp.data.pageSize;
        _this.disInfo.pageNum = resp.data.pageNum;
      })
    },
    async getDiscount() {
      //获取已设置的优惠信息
      const { data : res } = await axios.get('discount/')
      console.log(res.data)
      this.disInfo = res.data
      this.disInfo.disId = res.data.disId
      this.disInfo.seniorDiscount = res.data.seniorDiscount
      this.disInfo.midnightDiscount = res.data.midnightDiscount
    },
    saveDiscounts() {
      const discounts = {
        disId: this.addInfo.disId,
        disName: this.addInfo.disName,
        disOff: this.addInfo.disOff,
        disState: this.addInfo.disState,
      }
      console.log("保存优惠信息:", discounts)
      // 假设这里是一个axios请求将数据传回数据库
      axios.post('/discount', discounts)
        .then(response => {
          console.log(response.data)
        })
        .catch(error => {
          console.error(error)
        })
    },
    // 监听添加对话框的关闭事件
    addDialogClosed(){
      this.$refs.addFormRef.resetFields()
    },
    // 修改活动优惠信息并提交
    editDisInfo(){
      this.$refs.editInfoRef.validate(async valid => {
        const _this = this
        if (!valid) return
        let success = true
        axios.defaults.headers.put['Content-Type'] = 'application/json'
        console.log('123');
        await axios.put('discount/', JSON.stringify(_this.editInfo)).then(resp => {
          if (resp.data.code !== 200){
            this.$message.error('修改影厅信息失败！')
            success = false
          }
        })
        console.log('123');
        if (!success) return
        this.editDialogVisible = false
        this.getDisList()
        this.$message.success('修改影厅信息成功！')
      })
    },
    // 显示修改对话框，回显数据
    showEditDialog(row){
      console.log(row);
      const id = row.disId;
      const _this = this
      axios.get('discount/' + id ).then(resp => {
        console.log(resp)
        _this.editInfo = resp.data.data
      })
      this.editDialogVisible = true
    },
  }
}

</script>

<style scoped>
/* 添加样式 */
.bill-discount {
  font-family: Arial, sans-serif;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.title {
  font-size: 24px;
  margin-bottom: 20px;
}

.discount-form {
  display: flex;
  flex-direction: column;
}

.form-group {
  margin-bottom: 15px;
}

label {
  font-size: 18px;
}

input[type="number"] {
  padding: 8px;
  border-radius: 5px;
  border: 1px solid #ccc;
  width: 100px;
}

.save-button {
  font-size: 18px;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: #fff;
  cursor: pointer;
}

.save-button:hover {
  background-color: #0056b3;
}
</style>
