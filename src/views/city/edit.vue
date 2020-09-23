<template>
  <div class="app-container">
    <el-row>
      <el-col :span="12">
        <el-form ref="form" label-width="80px">
          <el-form-item label="城市名称">
            <el-input v-model="city.name" @input="getFistLeter"></el-input>
          </el-form-item>
          <el-form-item label="索引">
            <el-input v-model="city.index"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit(id)">立即修改</el-button>
            <el-button @click="cancel">取消</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import axios from 'axios'
  import pinyin from 'pinyin'
  export default {
    name: 'EditCity',
    data() {
      return {
        city: {
          name: '',
          index: 'A'
        },
        id: this.$route.params.id
      }
    },
    methods: {
      getmes() {
        axios.get('/editing/' + this.id).then(res => {
          console.log(res)
          this.city.name = res.data.list.name,
            this.city.index = res.data.list.index
        })
      },
      // 重置
      cancel() {
        this.city.name = ''
        this.city.index = ''
      },
      // 提交添加
      onSubmit(id) {
        axios.put('/editcity/' + this.id,{
          index: this.city.index,
          name: this.city.name,
        }).then(res => {
          this.$message({
            message: '修改成功',
            type: 'success'
          })
          this.$router.push({
            path: `/city/list`
          })
        })
      },
      // 获取城市的第一个大写字符
      getFistLeter() {
        if (this.city.name) {
          var first = pinyin(this.city.name[0], {
            style: pinyin.STYLE_FIRST_LETTER
          })
          console.log(first)
          this.city.index = first[0][0].toUpperCase()
        }
      }
    },
    components: {},

    created() {
      this.getmes()
    }
  }
</script>
