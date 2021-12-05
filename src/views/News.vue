<template>
  <div style="padding:10px">
    <div style="margin: 10px 0">
      <el-button type="primary" @click="add">新增</el-button>
      <el-button type="primary">导入</el-button>
      <el-button type="primary">导出</el-button>
      <el-input v-model="search" placeholder="请输入关键字"  style="width: 20%;margin-left: 480px" clearable="true"/>
      <el-button type="primary" style="margin-left: 10px" @click="queryAllByLimit">查询</el-button>
    </div>
    <el-table :data="tableData"
              border stripe style="width: 100%">
      <el-table-column prop="checkbox"  width="50"  v-if="showCheckbox" type="selection"/>
      <el-table-column prop="id" label="ID"  sortable width="70"/>
      <el-table-column prop="title" label="标题"  />

      <el-table-column prop="author" label="作者"  />
      <el-table-column prop="time" label="时间"  />

      <el-table-column label="操作">
        <template #default="scope">
          <el-button size="mini" @click="detail(scope.row)">详情</el-button>
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

      <!--      新增新闻-->
      <el-dialog v-model="dialogVisible" title="增加新闻" width="50%" :before-close="handleClose">
        <el-form
            ref="ruleForm"
            :model="form"
            :rules="addRules"
            status-icon
            label-width="120px"
            class="demo-ruleForm">
          <el-form-item label="标题"  label-width="50px" prop="title">
            <el-input

                style="width: 50%"
                v-model="form.title"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item>
          <div id="div1"></div>

          <el-form-item>
            <el-button type="primary" @click="save">添加</el-button>
            <el-button @click="noSave">取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>

      <!--      编辑用户信息-->
      <el-dialog v-model="dialogEdit" title="编辑用户信息" width="50%" :before-close="handleClose">
        <el-form
            ref="ruleForm"
            :model="editForm"
            status-icon
            label-width="120px"
            class="demo-ruleForm">
          <el-form-item label="标题"  label-width="50px" prop="title">
            <el-input
                style="width: 80%"
                v-model="editForm.title"
                type="text"
                autocomplete="off">
            </el-input>
          </el-form-item>
          <el-form-item label="内容" label-width="50px" prop="content">
            <div id="div2"></div>
          </el-form-item>
          <el-form-item label="作者" label-width="50px" prop="author">
          <el-input
              style="width: 80%"
              v-model="editForm.author"
              type="text"
              autocomplete="off">
          </el-input>
        </el-form-item>


          <el-form-item>
            <el-button type="primary" @click="Edit">编辑</el-button>
            <el-button @click="noEdit">取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>

      <el-dialog v-model="dialogDetail" title="详细内容" width="50%" :before-close="handleClose">
        <el-card>
          <div v-html="detailMsg.content" style="min-height: 100px"></div>

        </el-card>
      </el-dialog>
    </div>
  </div>

</template>

<script>
import request from "../utils/request";
import E from 'wangeditor'
let editor
export default {
  name: 'News',
  components: {
  },
  data() {

    return {
      fill:'fill',
      detailMsg:'',
      fileList:[],
      editRow:{
      },
      editForm:{

      },
      showCheckbox:false,
      search:'',
      form:{

      },
      dialogDetail:false,
      dialogVisible:false,
      dialogEdit:false,
      total:10,
      pageSize:10,
      currentPage:1,
      tableData: [
      ],
      addRules:{

      }
    }
  },

  created() {
    this.queryAll()

  },

  methods:{
    detail(row){
      this.dialogDetail=true
      console.log(row.content)
      this.detailMsg=row
    },
    handleSizeChange(val) {
      this.pageSize=val;
      this.queryAllByLimit()
    },
    handleCurrentChange(val) {
      this.currentPage=val;
      this.queryAllByLimit()
    },

    add(){
      this.form={};
      this.dialogVisible = true;
      this.$nextTick(()=>{
        editor = new E('#div1')
        editor.config.uploadImgServer = 'http://localhost:8001/files/edit/upload'
        editor.config.uploadFileName = "file"
        editor.create()
      })
    },
    handleEdit(row){
      this.dialogEdit=true;
      this.$nextTick(()=>{
        editor = new E('#div2')
        editor.create()
        editor.txt.html(row.content)
      })
      this.editForm=row;
      this.editRow=row;

    },
    handleDelete(id,index,rows){

      console.log(id)
      request.post('/news/deleteById',id).then(res =>{
        rows.splice(index, 1)
        if (res.status=="0"){
          this.$message({
            type: 'success',
            message: '删除成功'
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
    Edit(){
      console.log(this.editForm)
      request.post("/news/update",this.editForm).then(res =>{
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
      let user=JSON.parse(sessionStorage.getItem('user'))||"{}"
      console.log(user)
      this.form.content=editor.txt.html()
      this.form.author=user.nickName
      console.log(this.form)
      request.post("/news/save",this.form).then(res =>{
        this.dialogVisible=false;
        this.form={};
        if (res.status=="0"){
          this.queryAllByLimit()
          this.$message({
            type: 'success',
            message: '添加成功'

          })
          editor.txt.clear()
        }else {
          this.$message({
            type: 'error',
            message: res.message
          })
        }
      })

    },
    queryAll(){
      request.get('/news/queryAll',{
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

      request.get('/news/queryAllByLimit',{
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
