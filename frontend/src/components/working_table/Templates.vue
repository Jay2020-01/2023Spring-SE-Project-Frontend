<template>
  <div class="w">
    <!-- 排版 -->
    <h1>选择你的模板</h1>
    <el-row :gutter="40">
      <!-- 上面这个gutter是改变模板卡片之间的间隔 -->
      <el-col style="width:200px">
        <el-card :body-style="{ padding: '0px' } " shadow="hover">
          <!-- <img src="../../assets/templates_photos/v1.png" class="image" style="width:200px;height:120px;"> -->
          <el-image
            style="width:200px;height:120px;"
            :src="require('../../assets/templates_photos/v1.png')"
            :preview-src-list="srclist"
          ></el-image>
          <div style="padding: 14px;">
            <span>健身管理模板</span>
            <div class="bottom clearfix">
              <el-button type="text" class="button" @click="newdocVisible=true;chosen_temp=1;">使用这个</el-button>
            </div>
          </div>
        </el-card>
      </el-col>
      <el-col style="width:200px">
        <el-card :body-style="{ padding: '0px' }" shadow="hover">
          <!-- <img src="../../assets/templates_photos/v2.png" class="image" style="width:200px;height:120px;"> -->
          <el-image
            style="width:200px;height:120px;"
            :src="require('../../assets/templates_photos/v2.png')"
            :preview-src-list="srclist"
          ></el-image>
          <div style="padding: 14px;">
            <span>读书笔记模板</span>
            <div class="bottom clearfix">
              <el-button type="text" class="button" @click="newdocVisible=true;chosen_temp=2;">使用这个</el-button>
            </div>
          </div>
        </el-card>
      </el-col>
      <el-col style="width:200px">
        <el-card :body-style="{ padding: '0px' }" shadow="hover">
          <!-- <img src="../../assets/templates_photos/v3.png" class="image" style="width:200px;height:120px;"> -->
          <el-image
            style="width:200px;height:120px;"
            :src="require('../../assets/templates_photos/v3.png')"
            :preview-src-list="srclist"
          ></el-image>
          <div style="padding: 14px;">
            <span>个人简历模板</span>
            <div class="bottom clearfix">
              <el-button type="text" class="button" @click="newdocVisible=true;chosen_temp=3;">使用这个</el-button>
            </div>
          </div>
        </el-card>
      </el-col>
      <el-col style="width:200px">
        <el-card :body-style="{ padding: '0px' }" shadow="hover">
          <!-- <img src="../../assets/templates_photos/v4.png" class="image" style="width:200px;height:120px;"> -->
          <el-image
            style="width:200px;height:120px;"
            :src="require('../../assets/templates_photos/v4.png')"
            :preview-src-list="srclist"
          ></el-image>
          <div style="padding: 14px;">
            <span>梦想计划清单</span>
            <div class="bottom clearfix">
              <el-button type="text" class="button" @click="newdocVisible=true;chosen_temp=4;">使用这个</el-button>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <!-- 隐藏的新建文件的表单 -->
    <el-dialog title="新建文档" :visible.sync="newdocVisible">
      <el-form ref="docForm" :model="docForm" label-width="80px">
        <el-form-item label="文档名称">
          <el-input v-model="docForm.name" placeholder="无标题" />
          <!-- <el-button style="text-align: left;">使用模板</el-button> -->
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="newdocVisible = false">取 消</el-button>
        <el-button type="primary" @click="newdocVisible = false; create_doc_with_temp();">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import axios from "axios";
import Qs from "qs";
export default {
  data() {
    return {
      newdocVisible: false,
      docForm: {
        name: "",
      },
      chosen_temp: 0,
      srclist: [
        require("../../assets/templates_photos/v1.png"),
        require("../../assets/templates_photos/v2.png"),
        require("../../assets/templates_photos/v3.png"),
        require("../../assets/templates_photos/v4.png"),
      ],
      teamList: [],
    };
  },
  created: function () {
    this.getTeamList();
  },
  methods: {
    async create_doc_with_temp() {
      // var myDate = new Date();
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
      try {
        const resp = await this.get_doc_id(this.chosen_temp);
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
    },
    get_doc_id(temp_id) {
      return new Promise((resolve, reject) => {
        var team_id = parseInt(this.$route.params.team_id);
        if (team_id >= 0) {
          console.log("用模板新建团队文档");
        } else {
          team_id = -1;
          console.log("用模板新建个人文档");
        }
        var data = Qs.stringify({
          title: this.docForm.name,
          team_id: team_id,
          temp_id: temp_id,
          // create_time: myDate.toLocaleString(),
        });
        axios
          .post("http://localhost:8000/ajax/create_doc_with_temp/", data)
          .then((resp) => {
            resolve(resp);
          })
          .catch((err) => {
            reject(err);
          });
      });
    },
    getTeamList() {
      axios.post("http://localhost:8000/ajax/get_my_team/").then((res) => {
        this.teamList = res.data.team_list;
      });
    },
  },
};
</script>
<style>
/* 布局测试 */
.ceshi {
  background: #99a9bf;
  border-color: black;
}
.el-card:hover {
  cursor: pointer;
  border: 1px solid #409eff;
}
</style>