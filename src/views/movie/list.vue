<template>
  <div class="app-container">
    <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
      type="index"
      label="序号"
      width="100">
    </el-table-column>
      <el-table-column
        prop="imgurl"
        label="电影图片"
        width="110">
          <template slot-scope="scope">
          <img :src="scope.row.imgurl"  min-width="70" height="70" />
          </template>
      </el-table-column>
       <el-table-column
        prop="title"
        label="电影名字"
        width="100">
      </el-table-column> <el-table-column
        prop="stars"
        label="演员"
        width="180">
      </el-table-column>
      <el-table-column
        prop="ratings"
        label="评分"
        width="110">
      </el-table-column>
      <el-table-column
        prop="description"
        label="描述"
        width="110">
      </el-table-column>
      <el-table-column
        prop="p.name"
        label="上映地区"
        width="110">
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="handleEdit(scope.row._id)">编辑</el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.row._id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      background
      layout="prev, pager, next"
      :page-size="pagesize"
      :current-page="currentPage"
      @current-change="changePage"
      :total="total">
    </el-pagination>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'MovieList',
  components: {  },
  data() {
    return {
      currentPage: 1,
      pagesize: 3,
      total: 0,
      numm: 0,
      tableData: []
    }
  },
  computed: {
    count(){
      return this.numm
    }
  },
  watch: {
    currentPage(newVal, oldVal) {
      if(newVal != oldVal){
        this.numm = (newVal-1)*this.pagesize
      } else {
        this.numm = 0
      }
    }
  },
  created() {
    this.getCitys()
  },
  methods: {
    getNum() {
      ++this.numm
      return this.numm
    },
    // 获取全部城市数据
    // getCitys() {
    //   axios.get('/citys').then(res => {
    //     console.log(res)
    //     if(res.data.code === 20000){
    //       this.tableData = res.data.list
    //     }
    //   })
    // },
    getCitys() {
      axios.get(`/movies?page=${this.currentPage}&pagesize=${this.pagesize}`).then(res => {
        this.tableData = res.data.list
        this.total = res.data.total
      })
    },
    handleEdit(id) {
      this.$router.push({
        path: '/movie/edit/' + id
      })
    },
    handleDelete(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          axios.delete('/delmovie/'+id).then(res => {
            console.log(res)
            this.$message({
              type: 'success',
              message: res.data.msg
            });
            this.getCitys()
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });

    },
    changePage(page) {
      this.currentPage = page
      this.getCitys()
    }
  }
}
</script>

<style scoped>

</style>
