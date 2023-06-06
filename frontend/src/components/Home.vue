<template>
  <el-container
    class="home-container"
    :style="styleControl ? 'filter: invert(95%);' : ''"
  >
    <!-- 头部区域 -->
    <el-header
      height="60px"
      direction="horizontal"
      :style="styleControl ? 'margin-bottom: 0px;' : 'margin-bottom: 7px;'"
    >
      <el-row type="flex" class="row-bg">
        <el-col :span="8" :offset="0">
          <div class="grid-content head-box1 bg-purple">
            <!-- 头像区域 -->
            <el-avatar
              icon="fa fa-diamond"
              style="
                color: #409eff;
                background-color: #fff !important;
                cursor: pointer;
              "
              :size="40"
              @click.native="backtoHome"
              @click.middle.native="styleControl = !styleControl"
              >logo</el-avatar
            >
            <span class="site-name">FloK-8：流程日志挖掘工具</span>
          </div>
        </el-col>
        
      </el-row>
    </el-header>
    <!-- 页面主体区域 -->
    <el-container>
      <!-- 侧边栏 -->
      <el-aside width="250px">
        <!-- 侧边栏滑动调节 -->
        <el-row>
          <!-- 左边的滑动条 -->
          <el-col
            :span="12"
            style="
              display: flex;
              flex-direction: column;
              justify-content: center;
              align-items: center;
            "
          >
            <span
              style="
                height: 20px;
                font-size: 16px;
                display: flex;
                align-items: center;
              "
              >Activities</span
            >
            <el-row style="margin-top: 15px">
              <el-slider
                v-model="sliderValue1"
                vertical
                height="380px"
                :show-input="true"
                :show-input-controls="false"
              >
              </el-slider>
            </el-row>
          </el-col>
          <!-- 右边的滑动条 -->
          <el-col
            :span="12"
            style="
              display: flex;
              flex-direction: column;
              justify-content: center;
              align-items: center;
            "
          >
            <span
              style="
                height: 20px;
                font-size: 16px;
                display: flex;
                align-items: center;
              "
              >Paths</span
            >
            <el-row style="margin-top: 15px">
              <el-slider
                v-model="sliderValue2"
                vertical
                height="380px"
                :show-input="true"
                :show-input-controls="false"
              >
              </el-slider>
            </el-row>
          </el-col>
        </el-row>
        <!-- 侧边栏选项卡 -->
        <el-row>
          <el-collapse v-model="activeName" accordion>
            <!-- 上面的Frequency选项卡 -->
            <el-collapse-item title="Frequency" name="1" style="">
              <el-row style="display: flex; justify-content: center">
                <el-select v-model="value1" placeholder="请选择" style="width: 180px; ">
                <el-option
                  v-for="item in options1"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
              </el-row>
            </el-collapse-item>
            <!-- 下面的Performance选项卡 -->
            <el-collapse-item title="Performance" name="2">
              <el-row style="display: flex; justify-content: center">
                <el-select v-model="value2" placeholder="请选择" style="width: 180px; ">
                <el-option
                  v-for="item in options2"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
              </el-row>
            </el-collapse-item>
          </el-collapse>
        </el-row>
      </el-aside>
      <!-- 右侧内容主体 -->
      <el-main>
        <!-- 显示图片 -->
        <el-image :src="src" style="width: 95%; height: 95%"></el-image>

        <!-- 路由占位符 -->
        <router-view />
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import axios from "axios";
import Qs from "qs";
export default {
  data() {
    return {
      // 滑块数值
      sliderValue1: 100,
      sliderValue2: 100,
      activeName: "1",
      // 图片url
      src: "https://cube.elemecdn.com/6/94/4d3ea53c084bad6931a56d5158a48jpeg.jpeg",
      // 
      options1: [{
          value: '1',
          label: 'Absolute frequency'
        }, {
          value: '2',
          label: 'Case frequency'
        }, {
          value: '3',
          label: 'Max. repetitions'
        }, {
          value: '4',
          label: 'Case coverage'
        }],
      value1: '1',
      // 
      options2: [{
          value: '1',
          label: 'Total duration'
        }, {
          value: '2',
          label: 'Median duration'
        }, {
          value: '3',
          label: 'Mean duration'
        }, {
          value: '4',
          label: 'Max. duration'
        }, {
          value: '5',
          label: 'Min. duration'
        }],
      value2: '1',


      // 搜索文档关键词
      input: "",
      // 默认激活
      activeIndex: "/workingTable",
      dialogFormVisible: false,
      newdocVisible: false,
      // 新建团队时提交的团队名表单
      teamForm: {
        name: "",
      },
      // 存储团队信息
      teamList: [],
      docForm: {
        name: "",
        authority: [],
        // 如果不用在新建的时候设置权限就把上面这个删了
      },
      formLabelWidth: "120px",
      // 改：根据登陆人员的的信息改(可能是表单形式)
      username: "111111111111111111",
      mail_address: "11111111111111111",
      imageUrl:
        "https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png",
      // 消息列表
      noticeList: [],
      // 彩蛋
      styleControl: false,
      names: "Designed by 纪怀宇 黄俊钦 沙斌竹 陈新钰 李岱泉 姜昊",
    };
  },
  created: function () {
    this.getTeamList();
    this.get_user_avatar();
    this.get_user_info();
    this.getNoticeList();
  },
  // activated: function() {
  //   this.getTeamList();
  // },
  beforeUpdate: function () {
    this.get_user_avatar();
  },
  methods: {
    // 拉取用户头像
    get_user_avatar() {
      axios.get("http://localhost:8000/ajax/get_user_avatar/").then((res) => {
        this.imageUrl = res.data.url;
      });
    },
    // 拉取用户名和邮箱地址
    get_user_info() {
      axios.get("http://localhost:8000/ajax/user_info/").then((res) => {
        this.username = res.data.username;
        this.mail_address = res.data.mail_address;
        this.imageUrl = res.data.url;
      });
    },
    createTeam(formName) {
      // 验证表单
      this.$refs[formName].validate((valid) => {
        if (valid) {
          var data = Qs.stringify(this.teamForm); // 先用Qs对数据进行处理
          axios
            .post("http://localhost:8000/ajax/create_team/", data)
            .then((res) => {
              this.getTeamList();
            });
        } else {
          alert("表格不能为空");
        }
        // 强制刷新
        // this.$router.go(0);
        // this.activeIndex = "/teamSpace";
      });
    },
    // 获取团队名列表
    getTeamList() {
      axios.post("http://localhost:8000/ajax/get_my_team/").then((res) => {
        this.teamList = res.data.team_list;
      });
    },
    // 判断能否新建文档
    canNewDoc() {
      var team_id = parseInt(this.$route.params.team_id);
      if (typeof this.$route.params.team_id === "undefined") {
        console.log("is nan");
        this.newdocVisible = true;
      } else {
        for (var i = 0; i < this.teamList.length; i++) {
          if (team_id == this.teamList[i].team_id) {
            if (this.teamList[i].level < 4) {
              this.$message({
                showClose: true,
                message: "您的权限不能创建文档",
                type: "error",
              });
              break;
            } else {
              this.newdocVisible = true;
            }
          }
        }
      }
    },
    // 获取消息列表
    getNoticeList() {
      console.log("success");
      axios.get("http://localhost:8000/ajax/get_user_notice/").then((res) => {
        this.noticeList = res.data.notice_list;
      });
    },
    // 从消息列表统意/拒绝邀请
    responseInvitation(index, answer) {
      var data = Qs.stringify({
        notice_id: this.noticeList[index].notice_id,
        answer: answer,
        team_id: this.noticeList[index].target_id,
      });
      console.log(data);
      axios
        .post("http://localhost:8000/ajax/response_invitation/", data)
        .then((res) => {});
    },
    logout() {
      this.$store.dispatch("logout").then(() => {
        this.$router.push("/login");
      });
    },
    changeInfo() {
      window.sessionStorage.clear();
      this.$router.push("/myinfo");
    },
    async newFile() {
      // var myDate = new Date();
      // 限制必须输入文档标题
      if (this.docForm.name.length == 0) {
        this.$message({
          showClose: true,
          message: "请输入文档标题",
          type: "error",
        });
      } else {
        this.newdocVisible = false;
        window.sessionStorage.clear();
        var doc_id = 0;
        var team_id = parseInt(this.$route.params.team_id);
        var level = 4;
        // console.log(this.teamList)
        if (team_id >= 0) {
          level = this.teamList.find((team) => team.team_id == team_id).level;
        } else {
          team_id = -1;
        }
        // console.log(myDate.toLocaleString());
        // console.log(level)
        try {
          const resp = await this.get_docid();
          console.log(resp);
          const flag = resp.data.flag;
          doc_id = resp.data.doc_id;
          if (flag == "yes") {
            this.$message({
              message: "新建成功",
              type: "success",
            });
            var data = Qs.stringify({
              doc_id: doc_id,
            });
            axios.post("http://localhost:8000/ajax/update_browsing/", data);
          } else {
            this.$message({
              message: "新建文档出错",
              type: "warning",
            });
          }
        } catch (err) {
          console.log(err);
        }
        // console.log(doc_id);
        this.$router.push("/editor/" + doc_id + "/" + team_id + "/" + level);
      }
    },
    get_docid(data) {
      return new Promise((resolve, reject) => {
        var team_id = parseInt(this.$route.params.team_id);
        if (team_id >= 0) {
          console.log("新建团队文档");
        } else {
          team_id = -1;
          console.log("新建个人文档");
        }
        var data = Qs.stringify({
          title: this.docForm.name,
          team_id: team_id,
          // create_time: myDate.toLocaleString(),
        });
        axios
          .post("http://localhost:8000/ajax/create_doc/", data)
          .then((resp) => {
            resolve(resp);
          })
          .catch((err) => {
            reject(err);
          });
      });
    },
    backtoHome() {
      window.sessionStorage.clear();
      this.$router.push("/home");
    },
    use_templates() {
      // this.$message({
      // showClose:true,
      //     message: "请跳转到选择模板页面",
      //     type: "warning",
      //   });
      var team_id = -1;
      if (typeof this.$route.params.team_id !== "undefined")
        team_id = this.$route.params.team_id;
      this.$router.push("/templates/" + team_id);
    },

    // 搜索文档
    searchFile() {
      if (this.input.length != 0)
        this.$router.push("/searchresult/" + this.input);
    },
  },
};
</script>

<style lang="less" scoped>
.home-container {
  height: 100%;
  // filter: invert(95%);
}

.el-header {
  background-color: #ffffff;
  color: #333;
  text-align: left;
  line-height: 60px;
  border: 1px solid #eee;
  box-shadow: 0 0 10px #ddd;
  align-items: center;
}

.el-aside {
  padding-top: 30px;
  background-color: #ffffff;
  color: #333;
  text-align: left;
  line-height: 200px;
  border-right: 1px solid #eee;
  .el-menu {
    border-right: none;
  }
}

.el-main {
  background-color: #ffffff;
  color: #333;
  text-align: center;
}

body > .el-container {
  margin-bottom: 40px;
}

// 顶栏内容样式
.head-box1 {
  display: flex;
  align-items: center;
}
.el-avatar {
  margin: 10px 15px 10px 30px;
}
.site-name {
  font-size: 20px;
  color: #409eff;
}
.head-box3 {
  display: flex;
  align-items: center;
  height: 60px;
  box-sizing: border-box;
  // 居右对齐
  justify-content: flex-end;
  // border: 1px solid red;
}

// .bg-purple {
//   background: #d3dce6;
// }
// .bg-purple-light {
//   background: #e5e9f2;
// }
// .grid-content {
//   border-radius: 4px;
//   max-height: 30px;
// }
.row-bg {
  align-items: center;
  // margin: 2.5px 0;
  // background-color: #f9fafc;
}

// 二级菜单样式
.second-menu {
  padding-left: 50px !important;
  color: #7a7d81 !important;
  font-size: 12px !important;
}
// 新增团队按钮
.add-team {
  padding: 0 10px;
}
.add-team:hover {
  transform: scale(1.4);
}
.new-doc {
  width: 199px;
  height: 56px;
  line-height: 56px;
  padding-left: 30px;
  // background-color: blue;
  // text-align: center;
  display: table-cell;
  vertical-align: middle;
}
.el-dropdown-link {
  cursor: pointer;
  color: #409eff;
}
.el-icon-arrow-down {
  font-size: 12px;
}
// 卡片样式
.el-card {
  margin: 5px;
}
// 卡片内容样式
.card-container {
  align-items: center;
  display: flex;
  height: 50px;
  width: 300px;
}
.inline-div {
  display: inline-block;
}
.picture {
  box-sizing: border-box;
  align-items: center;
  width: 15%;
}
.word {
  width: 80%;
  text-align: left;
}
.title {
  font-size: 12px;
}
.details {
  margin-top: 3px;
  font-size: 10px;
  color: #999;
}
.show-names {
  font-size: 13px;
}
</style>
