<template>
  <el-row class="menu_page">
    <el-col>
      <el-menu
      unique-opened
        mode="vertical"
        background-color="#324057"
        text-color="#fff"
        active-text-color="#409eff"
        class="el-menu-vertical-demo"
      >
        <router-link to="/home">
          <el-menu-item index="0">
            <i class="fa fa-margin fa-server"></i>
            <span slot="title">首页</span>
          </el-menu-item>
        </router-link>
        <template v-for="(item,index) in items">
          <el-submenu
            v-if="item.children"
            :index="(index+1).toString()"
            :key="(index+1).toString()"
          >
            <template slot="title">
              <i :class="'fa fa-margin '+item.icon"></i>
              <span slot="title">{{item.name}}</span>
            </template>
            <router-link v-for="(citem,cindex) in item.children" :to="citem.path" :key="cindex">
              <el-menu-item :index="citem.path">
                <i :class="'fa fa-margin '+citem.icon"></i>
                <span slot="title">{{citem.name}}</span>
              </el-menu-item>
            </router-link>
          </el-submenu>
          <router-link v-else :to="item.path" :key="item.path">
            <el-menu-item :index="(index+1).toString()">
              <i :class="'fa fa-margin '+item.icon"></i>
              <span slot="title">{{item.name}}</span>
            </el-menu-item>
          </router-link>
        </template>
      </el-menu>
    </el-col>
  </el-row>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  name: "leftmenu",
  data() {
    return {
      items: []
    };
  },
  created() {
    let data = [
      {
        icon: "fa-money",
        name: "试卷管理",
        type: "1",
        children: [
          {
            icon: "fa-search-minus",
            name: "查阅试卷",
            path: "/consultQuestions",
            type: "1-1"
          },
          {
            icon: "fa-google-plus",
            name: "发布试卷",
            path: "/addQuestion",
            type: "1-2"
          },
          {
            icon: "fa-file-code-o",
            name: "已发布试卷",
            path: "/publishedQuestions",
            type: "1-3"
          },
          {
            icon: "fa-text-width",
            name: "填写的试卷",
            path: "/answeredList",
            type: "1-4"
          }
        ]
      },
      {
        icon: "fa-area-chart",
        name: "试题管理",
        type: "2",
        children: [
          {
            icon: "fa-crosshairs",
            name: "试题库",
            path: "/assembleQuestion",
            type: "2-1"
          },
          {
            icon: "fa-plus",
            name: "添加试题",
            path: "/itemBank",
            type: "2-2"
          },
          {
            icon: "fa-exclamation-triangle",
            name: "我的错题",
            path: "/myMistake",
            type: "2-3"
          },
          {
            icon: "fa-file-excel-o",
            name: "练习题",
            path: "/exercises",
            type: "2-4"
          }
        ]
      },
      {
        icon: "fa-asterisk",
        name: "试卷统计",
        path: "/questionsStatistics",
        type: "3"
      },
      {
        icon: "fa-comment",
        name: "我的评论",
        path: "/myCommentList",
        type: "4"
      },
      {
        icon: "fa-compress",
        name: "我的收藏",
        path: "/collectionQuestion",
        type: "5"
      },
      {
        icon: "fa-user",
        name: "用户管理",
        path: "/userMessage",
        type: "6"
      }
    ];
    let jurisdiction = this.userInfo._id ? this.userInfo.jurisdiction : [];
    let arr = [];
    data.forEach(item => {
      if (jurisdiction.includes(item.type)) {
        let obj = {
          name: item.name,
          icon: item.icon,
          path: item.path,
          type: item.type
        };
        if (item.children && item.children.length != 0) {
          obj.children = [];
          item.children.forEach(i => {
            if (jurisdiction.includes(i.type)) {
              let obj1 = {
                name: i.name,
                icon: i.icon,
                path: i.path,
                type: i.type
              };
              obj.children.push(obj1);
            }
          });
        }
        arr.push(obj);
      }
    });
    this.items = arr;
  },
  computed: {
    ...mapGetters(["userInfo"])
  }
};
</script>
<style scoped>
.menu_page {
  position: fixed;
  top: 81px;
  left: 0;
  min-height: 100%;
  background-color: #324057;
  z-index: 99;
}
.el-menu {
  border: none;
}
.fa-margin {
  margin-right: 9px;
}
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 180px;
  min-height: 400px;
}
.el-menu-vertical-demo {
  width: 35px;
}
.el-submenu .el-menu-item {
  min-width: 180px;
}

.hiddenDropdown,
.hiddenDropname {
  display: none;
}
a {
  text-decoration: none;
}
</style>
