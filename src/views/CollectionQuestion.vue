<template>
    <div class="my-container">
        <div class="flex-row flex-wrap">
            <div class="flex-row flex-center min-width mt10">
            <span class="text-nowrap pr10 pl10 font18">试卷名称 : </span>
            <el-input
                v-model="title"
                placeholder="请输入试卷名称"
                clearable>
            </el-input>
            </div>
            <div class="flex-row flex-center min-width mt10">
                <span class="text-nowrap pr10 pl10 font18">出卷人名称 : </span>
                <el-input
                    v-model="userName"
                    placeholder="请输入出卷人名称"
                    clearable>
                </el-input>
            </div>
            <div class="flex-row flex-center min-width mt10">
                <span class="text-nowrap pr10 pl10 font18">试卷类型 : </span>
                <el-select clearable v-model="questionType" placeholder="请选择试卷类型">
                <el-option label="常识" value="常识"></el-option>
                <el-option label="交通安全" value="交通安全"></el-option>
                <el-option label="法律知识" value="法律知识"></el-option>
                <el-option label="问卷调查" value="问卷调查"></el-option>
                <el-option label="在线考试" value="在线考试"></el-option>
                <el-option label="练习题" value="练习题"></el-option>
                </el-select>
            </div>
            <div class="flex-row flex-center min-width mt10">
                <span class="text-nowrap pr10 pl10 font18">发布时间 : </span>
                <el-date-picker
                    v-model="beginTime"
                    type="date"
                    placeholder="选择日期">
                </el-date-picker>
                <span class="pl10 pr10 font18">--</span>
                <el-date-picker
                    v-model="endTime"
                    type="date"
                    placeholder="选择日期">
                </el-date-picker>
            </div>
            <div class="flex-row flex-center ml10 mt10">
                <el-button type="primary" @click="search">查询</el-button>
            </div>
        </div>
        <div class="mt20">
            <el-table
                :data="questionsList"
                style="width: 100%">
                    <el-table-column
                    type="index"
                    width="50">
                </el-table-column>
                <el-table-column
                    prop="title"
                    align='center'
                    label="试卷名称">
                </el-table-column>
                <el-table-column
                    prop="singleCount"
                    align='center'
                    label="单选题数量">
                </el-table-column>
                <el-table-column
                    prop="multipleCount"
                    align='center'
                    label="多选题数量">
                </el-table-column>
                <el-table-column
                    prop="judgementCount"
                    align='center'
                    label="判断题数量">
                </el-table-column>
                <el-table-column
                    prop="answerCount"
                    align='center'
                    label="简答题数量">
                </el-table-column>
                <el-table-column
                    prop="totalCount"
                    align='center'
                    label="总数量">
                </el-table-column>
                <el-table-column
                    prop="questionType"
                    align='center'
                    label="试卷类型">
                </el-table-column>
                <el-table-column
                    prop="userName"
                    align='center'
                    label="发布者">
                </el-table-column>
                <el-table-column
                    prop="createTime"
                    align='center'
                    label="发布时间">
                    <template slot-scope="scope">
                        <div>{{scope.row.createTime | formatDate}}</div>
                    </template>
                </el-table-column>
                <el-table-column label="操作" align='center' width="200">
                    <template slot-scope="scope">
                        <el-button
                        v-if="!scope.row.isAnswer"
                        size="small"
                        type="success"
                        @click="handleClick(scope.$index, scope.row)">开始做题</el-button>

                        <el-button
                        v-if="scope.row.isAnswer"
                        size="small"
                        type="warning"
                        @click="handleDetail(scope.$index, scope.row)">查看详情</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>
        <div v-if="total>pageSize" class="text-center my-pagination">
            <el-pagination
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-size="pageSize"
                :layout='layout'
                :background='true'
                :total="total">
            </el-pagination>
        </div>
    </div>
</template>

<script>
import { get } from "../util/http.js";
import { mapGetters } from "vuex";
export default {
  data() {
    return {
      questionsList: [],
      pageSize: 10,
      currentPage: 1,
      total: 0,
      layout: "total, prev, pager, next,jumper",
      title: "",
      userName: "",
      beginTime: "",
      endTime: "",
      questionType: ""
    };
  },
  created() {
    this.getQuestionList();
  },
  methods: {
    async getQuestionList() {
      try {
        let params = {};
        if (this.beginTime && this.endTime && this.beginTime > this.endTime) {
          this.$message({
            showClose: true,
            message: "结束时间不能小于开始时间",
            type: "warning"
          });
          return;
        }
        if (this.beginTime) {
          params.beginTime = this.beginTime;
        }
        if (this.endTime) {
          params.endTime = this.endTime;
        }
        if (this.title) {
          params.title = this.title;
        }
        if (this.userName) {
          params.userName = this.userName;
        }
        if (this.questionType) {
          params.questionType = this.questionType;
        }
        params.pageSize = this.pageSize;
        params.currentPage = this.currentPage;
        params.userId = this.userInfo._id;
        params.checkList = this.userInfo.identity;
        const result = await get("/api/questions/getCollectQuestion", params);
        if (result.statusCode == 200) {
          this.questionsList = result.data.collectionList;
          this.total = result.data.total;
        } else {
          this.$message({
            showClose: true,
            message: result.message,
            type: "warning"
          });
        }
      } catch (error) {
        this.$message({
          showClose: true,
          message: error.toString(),
          type: "error"
        });
      }
    },
    handleClick(index, row) {
      this.$router.push({ name: "answerQuestion", params: { id: row._id },query: { isCollection: 1 } });
    },
    handleDetail(index, row) {
      this.$router.push({
        name: "answerDetail",
        params: { id: row.answerId },
      });
    },
    handleCurrentChange(e) {
      this.currentPage = e;
      this.getQuestionList();
    },
    search() {
      this.currentPage = 1;
      this.getQuestionList();
    }
  },
  computed: {
    ...mapGetters(["userInfo"])
  }
};
</script>

<style lang="less" scoped>
.my-container {
  padding: 20px;
  // position: relative;
  // height: calc(100% - 71px);
}
.min-width {
  min-width: 300px;
}
.text-nowrap {
  white-space: nowrap;
}
.my-pagination {
  // position: absolute;
  // bottom: 50px;
  // left: 0;
  // width: 100%;
  margin: 30px 0;
}
</style>


