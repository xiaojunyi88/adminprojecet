<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" class="separator">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>线上报名管理</el-breadcrumb-item>
    </el-breadcrumb>
    <div class="mod-log">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-row>
          <el-col :span="21">
            <div class="grid-content bg-purple">
              <el-row>
                <el-col :span="4">
                  <div class="grid-content bg-purple">
                    <el-form-item>
                      <el-input
                        v-model="dataForm.key"
                        placeholder="就业项目名称"
                        style="width:10vw"
                        clearable
                      ></el-input>
                    </el-form-item>
                  </div>
                </el-col>
                <el-col :span="3">
                  <div class="grid-content bg-purple-light">
                    <el-form-item>
                      <el-button @click="getDataList()">查询</el-button>
                    </el-form-item>
                  </div>
                </el-col>
                <el-col :span="16"></el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="3">
            <div class="grid-content bg-purple-light">
              <el-button @click="addProject" icon="el-icon-plus" type="primary">新增就业项目</el-button>
            </div>
          </el-col>
        </el-row>
      </el-form>

      <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
        <el-table-column prop="skillName" header-align="center" align="center" label="就业项目名称"></el-table-column>
        <el-table-column prop="persionNum" header-align="center" align="center" label="报名人数"></el-table-column>

        <el-table-column prop="skillSort" header-align="center" align="center" label="排序"></el-table-column>
        <el-table-column prop="createTime" header-align="center" align="center" label="创建时间"></el-table-column>
        <el-table-column prop="status" header-align="center" align="center" label="窗口状态"></el-table-column>
        <el-table-column prop="status" header-align="center" align="center" label="操作">
          <template slot-scope="scope">
            <el-button type="text" size="small" @click="queryByIDServiceItem(scope.row.skillId)">编辑</el-button>
            <el-button
              type="text"
              size="small"
              @click="pauseHandle(scope.row.status,scope.row.skillId)"
              v-if="scope.row.status=='停用'?!show:show"
            >停用</el-button>
            <el-button
              type="text"
              size="small"
              @click="resumeHandle(scope.row.status,scope.row.skillId)"
              v-else
            >启用</el-button>
            <!-- <el-button v-if="scope.row.businessStatus === '停用'" type="text" size="small">停用</el-button>
            <el-button v-else type="text" size="small">启用</el-button>-->
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="sizeChangeHandle"
        @current-change="currentChangeHandle"
        :current-page="pageIndex"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="pageSize"
        :total="totalPage"
        layout="total, sizes, prev, pager, next, jumper"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  inject: ["reload"],
  data() {
    return {
      skillId: "",
      show: true,
      dataForm: {
        key: ""
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      selectionDataList: []
    };
  },
  created() {
    this.getDataList();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;

      this.$http({
        url: this.$http.adornUrl("/sys/trainingSkill/querrySkillPage"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          skillName: this.dataForm.key
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          console.log(this.dataList);
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
      // this.reload();
    },
    // 暂停
    pauseHandle(id, skillId) {
      this.skillId = skillId;
      console.log(id, this.skillId);

      this.$confirm(`确定进行禁用操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/sys/trainingSkill/changeStatus"),
            method: "post",
            data: this.$http.adornData({
              skillId: this.skillId,
              businessStatus: "停用"
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.getDataList();
                }
              });
            } else {
              this.$message.error(data.msg);
            }
          });
          if ((id = "停用")) {
            this.show = !this.show;
          }
        })
        .catch(() => {});
    },
    // 恢复
    resumeHandle(id, skillId) {
      this.skillId = skillId;
      console.log(id, this.skillId);
      this.$confirm(`确定进行启用操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/sys/trainingSkill/changeStatus"),
            method: "post",
            data: this.$http.adornData({
              skillId: this.skillId,
              businessStatus: "启用"
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.getDataList();
                }
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        })
        .catch(() => {});
    },
    //修改
    queryByIDServiceItem(skillId) {
      this.skillId = skillId;
      this.$router.push({
        name: "addAppointment",
        query: {
          skillId: this.skillId
        }
      });
    },
    //跳页
    addProject() {
      this.$router.push({ name: "addAppointment" });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    }
  }
};
</script>

<style lang="scss" scoped>
.mod-log {
  margin-top: 3vh;
}
</style>
