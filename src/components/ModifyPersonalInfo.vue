<template>
  <div>
    <el-row type="flex" justify="center" style="padding-top:2rem;">
      <el-col :lg="10">
        <el-form
          :rules="rules"
          ref="userInfo"
          :model="userInfo"
          label-width="10rem"
          class="demo-ruleForm"
        >
          <el-form-item label="用户名" prop="userName">
            <el-input v-model="userInfo.userName"></el-input>
          </el-form-item>
          <el-form-item label="个人介绍" prop="userDescription">
            <el-input type="textarea" v-model="userInfo.userDescription"></el-input>
          </el-form-item>
          <el-form-item label="电话号" prop="userPhoneNumber">
            <el-input v-model="userInfo.userPhoneNumber"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit('userInfo')">修改</el-button>
            <el-button @click="resetForm('ruleForm')">重置</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      userInfo: {},
      rules: {
        userName: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          { min: 3, max: 10, message: "长度在 3 到 10 个字符", trigger: "blur" }
        ],
        userDescription: [
          { required: true, message: "请输入个人描述", trigger: "blur" }
        ],
        userPhoneNumber: [
          { required: true, message: "请输入手机号码", trigger: "blur" },
          {
            min: 11,
            max: 11,
            message: "请检查手机号码格式是否正确",
            trigger: "blur"
          }
        ]
      }
    };
  },
  created: function() {
    this.userInfo = Object.assign({},this.$store.state.userInfo);
  },
  methods: {
    onSubmit(userInfo) {
      let that = this;
      this.$refs[userInfo].validate(valid => {
        if (valid) {
          console.log(that.userInfo);
          this.$axios
            .post("/updateUserInfo", that.userInfo)
            .then(function(res) {
              console.log(res);
              if (res.data.stateCode == 200) {
                that.$message({
                  message: "由于您修改了当前用户的信息，请重新登录",
                  type: "success"
                });
                that.$store.commit("Logout");
                that.$router.push({ name: "Login" });
                // for (let key in that.userInfo) {
                //   that.userInfo[key] = "";
                // }
              } else {
                that.$message({
                  message: res.data.msg,
                  type: "warning"
                });
              }
            });
        } else {
          that.$message({
            message: "请按要求输入信息",
            type: "warning"
          });
          return false;
        }
      });
    },
    resetForm() {
      for (let key in this.userInfo) {
        this.userInfo[key] = "";
      }
    }
  }
};
</script>

<style>
</style>
