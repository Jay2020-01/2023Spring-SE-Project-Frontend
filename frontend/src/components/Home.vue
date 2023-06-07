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
              icon="fa fa-list-alt"
              style="
                color: #409eff;
                background-color: #fff !important;
                cursor: pointer;
              "
              :size="40"
              @click.native="getResult"
              >logo</el-avatar
            >
            <span class="site-name" @click="changeSlider">FloK-8：流程日志挖掘工具</span>
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
                v-model="activityRate"
                vertical
                height="380px"
                :show-input="true"
                :show-input-controls="false"
                @change="changeSlider"
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
                v-model="pathRate"
                vertical
                height="380px"
                :show-input="true"
                :show-input-controls="false"
                @change="getResult"
              >
              </el-slider>
            </el-row>
          </el-col>
        </el-row>
        <!-- 侧边栏选项卡 -->
        <el-row>
          <el-collapse v-model="activeName" accordion @change="changeCollapse">
            <!-- 上面的Frequency选项卡 -->
            <el-collapse-item title="Frequency" name="1" style="">
              <el-row style="display: flex; justify-content: center">
                <el-select v-model="value1" placeholder="请选择" style="width: 180px; " @change="changeSelect">
                <el-option
                  v-for="item in options1"
                  :key="item.value"
                  :label="item.label"
                  :value="item.label"
                >
                </el-option>
              </el-select>
              </el-row>
            </el-collapse-item>
            <!-- 下面的Performance选项卡 -->
            <el-collapse-item title="Performance" name="2">
              <el-row style="display: flex; justify-content: center">
                <el-select v-model="value2" placeholder="请选择" style="width: 180px; " @change="changeSelect">
                <el-option
                  v-for="item in options2"
                  :key="item.value"
                  :label="item.label"
                  :value="item.label"
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
        <img :src="'data:image/png;base64,'+ images.url" style="height: 75%" alt>

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
      activityRate: 100,
      pathRate: 100,
      activeName: "1",
      // 图片url
      images:{
		    url: "",   //base64码
		  },
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
      value1: 'Absolute frequency',
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
      value2: 'Total duration',


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
    this.getResult();
  },

  methods: {
    // 新项目函数
    // 获得图片
    getResult() {

      var showType;
      if(this.activeName === "2") {
        showType = this.value2;
      } else {
        showType = this.value1;
      }
      var data = Qs.stringify({
        file_path: "ExampleLog.csv",
        show_type: showType,
        activity_rate: this.activityRate / 100,
        path_rate: this.pathRate / 100,
      });
      console.log(data);
      axios
        .post("http://127.0.0.1:5000/predict", data)
        .then((res) => {
          this.images.url = res.data;
        });
    },

    // 点击首页图标
    clickLogo() {
      this.getResult();
    },
    // 滑动滑块
    changeSlider() {
      this.getResult();
    },
    // 改变折叠面板
    changeCollapse() {
      this.getResult();
    },
    // 改变选择类型
    changeSelect() {
      this.getResult();
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
