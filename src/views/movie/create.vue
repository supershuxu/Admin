<template>
  <div class="app-container">
    <el-row>
      <el-col :span="12">
        <el-upload class="avatar-uploader" action="upp" :show-file-list="false" :http-request="UploadImage"
          :on-success="handleAvatarSuccess" :before-upload="beforeAvatarUpload">
          <img v-if="imageUrl" :src="imageUrl" class="avatar" style="width:100px;height:100px" />
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="12"> </el-col>
      <el-col :span="12">
        <el-form ref="form" label-width="80px">
          <el-form-item label="电影名称">
            <el-input v-model="movie.title" @input="getFistLeter"></el-input>
          </el-form-item>
          <el-form-item label="演员">
            <el-input v-model="movie.stars" @input="getFistLeter"></el-input>
          </el-form-item>
          <el-form-item label="选择城市">
            <el-select v-model="movie.region" placeholder="请选择活动区域">
              <el-option :label="item.name" :value="item._id" v-for="item in list" :key="item._id"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="评分">
            <el-input v-model="movie.ratings" @input="getFistLeter"></el-input>
          </el-form-item>
          <el-form-item label="描述">
            <el-input v-model="movie.description" @input="getFistLeter"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">立即创建</el-button>
            <el-button @click="cancel">取消</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import axios from "axios";
  import pinyin from "pinyin";
  export default {
    name: "CreateCity",
    data() {
      return {
        movie: {
          title: "",
          stars: "",
          imgurl: "",
          region: "",
          ratings: "",
          description: ""
        },
        list: [],
        imageUrl: ""
      };
    },
    created() {
      this.getCitys();
    },
    methods: {
      getCitys() {
        axios.get("/cityss").then(res => {
          this.list = res.data.list;
        });
      },
      // 重置
      cancel() {
        this.movie.title = "";
        this.movie.stars = "";
        this.movie.region = "";
        this.movie.ratings = "";
        this.movie.description = "";
      },
      // 提交添加
      onSubmit() {
        axios.post('/movie/create', {
          title: this.movie.title,
          imgurl: this.imageUrl,
          stars: this.movie.stars,
          ratings: this.movie.ratings,
          description: this.movie.description,
          p: this.movie.region
        })
         this.$message({
            message: '添加成功',
            type: 'success'
          })
        this.$router.push({
          path: '/movie/list'
        })

      },

      // 获取城市的第一个大写字符
      getFistLeter() {
        // if (this.city.name) {
        //   var first = pinyin(this.city.name[0], {
        //     style: pinyin.STYLE_FIRST_LETTER
        //   });
        //   console.log(first);
        //   this.city.index = first[0][0].toUpperCase();
        // }
      },
      UploadImage(param) {
        console.log(param); //file
        let uploadData = new FormData();
        uploadData.append("avatar", param.file); // 上传图片的接口  传上去后让后台返回一个地址
        axios.post("/upload", uploadData).then(res => {
          this.imageUrl = res.data.path;
        });
      },
      handleAvatarSuccess(res, file) {
        this.imageUrl = URL.createObjectURL(file.raw);
      },
      beforeAvatarUpload(file) {
        const isJPG = file.type === "image/jpeg";
        const isLt2M = file.size / 1024 / 1024 < 2;

        if (!isJPG) {
          this.$message.error("上传头像图片只能是 JPG 格式!");
        }
        if (!isLt2M) {
          this.$message.error("上传头像图片大小不能超过 2MB!");
        }
        return isJPG && isLt2M;
      }
    },
    components: {}
  };
</script>

<style scoped>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }

  .avatar-uploader .el-upload:hover {
    border-color: #409eff;
  }

  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }

  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
