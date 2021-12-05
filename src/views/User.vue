<template>
  <div style="padding:10px">
    <div style="margin: 10px 0">
      <el-button type="primary" @click="addUser">新增</el-button>
      <el-button type="primary">导入</el-button>
      <el-button type="primary">导出</el-button>
      <el-input v-model="search" placeholder="请输入关键字"  style="width: 20%;margin-left: 480px" clearable="true"/>
      <el-button type="primary" style="margin-left: 10px" @click="queryAllByLimit">查询</el-button>
    </div>
    <el-table :data="tableData"
              border stripe style="width: 100%">
      <el-table-column prop="checkbox"  width="50"  v-if="showCheckbox" type="selection"/>
      <el-table-column prop="id" label="ID"  sortable width="70"/>
      <el-table-column prop="username" label="用户名"  />
      <el-table-column prop="nickName" label="昵称"  />
      <el-table-column prop="role" label="角色"  />
      <el-table-column prop="age" label="年龄"  />
      <el-table-column prop="sex" label="性别"  />
      <el-table-column prop="address" label="地址" />
      <el-table-column label="操作">
        <template #default="scope">
          <el-button size="mini" @click="handleEdit(scope.row)">编辑</el-button>
          <el-popconfirm title="确认删除?" @confirm="handleDelete(scope.row.id,scope.$index, tableData)">
            <template #reference>
              <el-button
                  size="mini"
                  type="danger">删除</el-button>
            </template>
          </el-popconfirm>

        </template>
      </el-table-column>
    </el-table>
    <div class="demo-pagination-block">

      <el-pagination
          style="margin: 10px 0"
          :currentPage.sync="currentPage"
          :page-sizes="[5, 10, 20]"
          :page-size.sync="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange">
      </el-pagination>

<!--      新增用户-->
      <el-dialog v-model="dialogVisible" title="增加用户" width="30%" :before-close="handleClose">
        <el-form
            ref="ruleForm"
            :model="form"
            :rules="addRules"
            status-icon
            label-width="120px"
            class="demo-ruleForm">
          <el-form-item label="用户名" prop="username">
            <el-input
                style="width: 80%"
                v-model="form.username"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input
                style="width: 80%"
                v-model="form.password"
                type="password"
                autocomplete="off">
            </el-input>
          </el-form-item>

          <el-form-item label="昵称" prop="nickName">
            <el-input
                style="width: 80%"
                v-model="form.nickName"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item><el-form-item label="角色" prop="role">
          <el-input
              style="width: 80%"
              v-model="form.role"
              type="text"
              autocomplete="off">
          </el-input>
        </el-form-item>
          <el-form-item label="年龄" prop="age">
            <el-input
                style="width: 80%"
                type="number"
                v-model="form.age">
            </el-input>
          </el-form-item>
          <el-form-item label="性别" prop="sex">
            <el-radio v-model="form.sex" label="男">男</el-radio>
            <el-radio v-model="form.sex" label="女">女</el-radio>
          </el-form-item>
          <el-form-item label="地址" prop="address">
            <elui-china-area-dht  :leave="3" @change="onChange" ></elui-china-area-dht>
            <el-input
                style="width: 80%"
                type="text"
                v-model="form.address">
            </el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="save">添加</el-button>
            <el-button @click="noSave">取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>

<!--      编辑用户信息-->
      <el-dialog v-model="dialogEdit" title="编辑用户信息" width="30%" :before-close="handleClose">
        <el-form
            ref="ruleForm"
            :model="editForm"
            status-icon
            label-width="120px"
            class="demo-ruleForm">
          <el-form-item label="用户名" prop="username">
            <el-input
                style="width: 80%"
                v-model="editForm.username"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item>
          <el-form-item label="密码" prop="pass">
            <el-input
                style="width: 80%"
                v-model="editForm.password"
                maxlength="18"
                minlength="6"
                type="password"
                autocomplete="off">
            </el-input>
          </el-form-item>

          <el-form-item label="昵称" prop="nickName">
            <el-input
                style="width: 80%"
                v-model="editForm.nickName"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item>
          <el-form-item label="角色" prop="role">
            <el-input
                style="width: 80%"
                type="number"
                v-model="editForm.role">
            </el-input>
          </el-form-item>
          <el-form-item label="年龄" prop="age">
            <el-input
                style="width: 80%"
                type="number"
                v-model="editForm.age">
            </el-input>
          </el-form-item>
          <el-form-item label="性别" prop="sex">
            <el-radio v-model="editForm.sex" label="男">男</el-radio>
            <el-radio v-model="editForm.sex" label="女">女</el-radio>
          </el-form-item>
          <el-form-item label="地址" prop="address">
            <elui-china-area-dht  :leave="3" @change="onChange" ></elui-china-area-dht>
            <el-input
                style="width: 80%"
                type="text"
                v-model="editForm.address">
            </el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="Edit">编辑</el-button>
            <el-button @click="noEdit">取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>
    </div>
  </div>

</template>

<script>
import {  ref } from 'vue';
import { EluiChinaAreaDht } from 'elui-china-area-dht'
import request from "../utils/request";
const chinaData = new EluiChinaAreaDht.ChinaArea().chinaAreaflat
export default {
  name: 'User',
  components: {
     EluiChinaAreaDht,
  },
  data() {

    return {
      editRow:{},
      editForm:{
        username: '',
        password: '',
        nickname: '',
        age: '',
        sex: '',
        address: '',
      },
      showCheckbox:false,
      search:'',
      form:{
        username: '',
        password: '',
        nickName: '',
        age: '',
        sex: '',
        address: '',
      },
      dialogVisible:false,
      dialogEdit:false,
      total:10,
      pageSize:10,
      currentPage:1,
      tableData: [
      ],
      addRules:{
        username:[
          {
            required: true,
            message: '请输入用户名',
            trigger: 'blur',
          },
          {
            min: 3,
            max: 20,
            message: '长度应为为3~20',
            trigger: 'change',
          },

        ],
        password:[
          {
            required: true,
            message: '请输入密码',
            trigger: 'blur',
          },
          {
            min: 6,
            max: 18,
            message: '长度应为为6~18',
            trigger: 'change',
          }
        ],
        nickname:[
          {
            required: true,
            message: '请输入昵称',
            trigger: 'blur',
          },
          {
            min: 6,
            max: 20,
            message: '长度应为为6~20',
            trigger: 'change',
          }
        ],
        age:[
          {
            required: true,
            message: '请输入年龄',
            trigger: 'blur',
          },
          {
            min: 1,
            max: 150,
            message: '年龄应在1~150之间',
            trigger: 'change',
          }
        ],
        sex:[
          {
            required: true,
            message: '请输入昵称',
            trigger: 'blur',
          },

        ],
        address:[
          {
            required: true,
            message: '请输入地址',
            trigger: 'blur',
          },
          {
            min: 6,
            max: 100,
            message: '长度应为为6~100',
            trigger: 'change',
          }
        ],
      }
    }
  },
  created() {
    this.queryAll()
  },
  methods:{
    created(){
      console.log("12313123")
    },
    handleSizeChange(val) {
      this.pageSize=val;
      this.queryAllByLimit()
    },
    handleCurrentChange(val) {
      this.currentPage=val;
      this.queryAllByLimit()
    },

    addUser(){
      this.form={};
      this.dialogVisible = true;
    },
    handleEdit(row){
      this.dialogEdit=true;
      this.editForm=row;
      this.editRow=row;

    },
    handleDelete(id,index,rows){

      console.log(id)
      request.post('/user/deleteById',id).then(res =>{
          rows.splice(index, 1)
          if (res.status=="0"){
            this.$message({
              type: 'success',
              message: '删除成功'
            })
          }else {
            this.$message({
              type: 'error',
              message: res.message
            })
          }

         })
    },
    Edit(){
      request.post("/user/update",this.editForm).then(res =>{
        this.editRow=res
        this.dialogEdit=false;
        if (res.status=="0"){
          this.$message({
            type: 'success',
            message: '编辑成功'
          })
          this.queryAllByLimit()
        }else {
          this.$message({
            type: 'error',
            message: res.message
          })
        }

      })
    },
    noEdit(){
      this.editForm={};
      this.dialogEdit=false;
    },
    noSave(){
      this.form={};
      this.dialogVisible = false;
    },
    save(){

        request.post("/user/save",this.form).then(res =>{
          this.dialogVisible=false;
          this.form={};
          if (res.status=="0"){
            this.$message({
              type: 'success',
              message: '添加成功'
            })
          }else {
            this.$message({
              type: 'error',
              message: res.message
            })
          }
        })

    },
    queryAll(){

      request.get('/user/queryAll',{
        params:{
          pageNum:this.currentPage,
          pageSize:this.pageSize,
        }
      }).then(res =>{
        this.tableData=res.list
        this.total=res.total
      })
    },
    queryAllByLimit(){

        request.get('/user/queryAllByLimit',{
          params:{
            pageNum:this.currentPage,
            pageSize:this.pageSize,
            search:this.search
          }
        }).then(res =>{
          this.tableData=res.list
          this.total=res.total
        })
      }
  },
  setup() {
    function onChange(e) {
      const one = chinaData[e[0]]
      const two = chinaData[e[1]]
      console.log(one, two)
    }
    return {
      onChange,
    }
  },
}
</script>
