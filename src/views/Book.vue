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
      <el-table-column prop="bookname" label="书名"  />
      <el-table-column prop="bookcover" label="封面地址"  />
      <el-table-column  label="封面">
        <template #default="scope">
          <el-image
              style="width: 100px; height: 100px"
              :src= scope.row.bookcover
              :fit="fill"
              :preview-src-list=[scope.row.bookcover]
          ></el-image>
        </template>
      </el-table-column>

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

      <!--      新增书籍-->
      <el-dialog v-model="dialogVisible" title="增加书籍" width="30%" :before-close="handleClose">
        <el-form
            ref="ruleForm"
            :model="form"
            :rules="addRules"
            status-icon
            label-width="120px"
            class="demo-ruleForm">
          <el-form-item label="书名" prop="bookname">
            <el-input
                style="width: 80%"
                v-model="form.bookname"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item>
          <el-form-item label="封面地址" prop="bookcover">
            <el-upload
                action="http://localhost:8001/files/upload" :on-success="filesUploadSuccess"
                :file-list="fileList">
              <el-button  type="primary">上传</el-button>
              <template #tip>
              </template>
            </el-upload>
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
          <el-form-item label="书名" prop="username">
            <el-input
                style="width: 80%"
                v-model="editForm.bookname"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item>
          <el-form-item label="封面地址" prop="bookcover">
            <el-upload
                action="http://localhost:8001/files/upload" :on-success="filesUploadSuccess"
                :file-list="fileList">
              <el-button  type="primary">上传</el-button>
              <template #tip>
              </template>
            </el-upload>
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
import request from "../utils/request";

export default {
  name: 'Book',
  components: {
  },
  data() {

    return {
      fill:'fill',
      fileList:[],
      editRow:{
      },
      editForm:{

      },
      showCheckbox:false,
      search:'',
      form:{

      },
      dialogVisible:false,
      dialogEdit:false,
      total:10,
      pageSize:10,
      currentPage:1,
      tableData: [
      ],
      addRules:{
        bookname:[
          {
            required: true,
            message: '请输入书籍名',
            trigger: 'blur',
          },
          {
            min: 3,
            max: 20,
            message: '长度应为为3~20',
            trigger: 'change',
          },

        ],
        bookcover:[
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
    filesUploadSuccess(res){
        console.log(res)
        this.form.bookcover=res.data
        this.editForm.bookcover=res.data
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
      request.post('/book/deleteById',id).then(res =>{
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
      console.log(this.editForm)
      request.post("/book/update",this.editForm).then(res =>{
        console.log(res)
        this.dialogEdit=false;
        if (res.status=="0"){
          this.queryAllByLimit()
          this.editRow=res
          this.$message({
            type: 'success',
            message: '编辑成功'
          })
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

      request.post("/book/save",this.form).then(res =>{
        this.dialogVisible=false;
        this.form={};
        if (res.status=="0"){
          this.queryAllByLimit()
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
      request.get('/book/queryAll',{
        params:{
          pageNum:this.currentPage,
          pageSize:this.pageSize,
        }
      }).then(res =>{
        console.log(res)
        this.tableData=res.list
        console.log(this.tableData)
        this.total=res.total
      })
    },
    queryAllByLimit(){

      request.get('/book/queryAllByLimit',{
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

}
</script>
