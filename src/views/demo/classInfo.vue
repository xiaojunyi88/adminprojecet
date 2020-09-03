<template>
  <div>
    <table border="1" cellpadding="0" cellspacing="0" rules="none">
      <tr>
        <td align="center">班级名称</td>
        <td align="center" colspan="3">{{this.name}}</td>
        <!-- <td align="center">二月</td>
        <td align="center">三月</td>-->
      </tr>
      <tr>
        <td align="center">学习周期</td>
        <td align="center">{{this.days}}</td>
        <td align="center">班主任</td>
        <td align="center">{{this.master}}</td>
      </tr>
      <tr>
        <td align="center">学员数量</td>
        <td align="center">{{this.classNum}}</td>
        <td align="center">备注</td>
        <td align="center">{{this.remark}}</td>
      </tr>
      <tr>
        <td align="center">培训种类</td>
        <td align="center" colspan="3">{{this.dataNames}}</td>
      </tr>
      <tr>
        <td align="center">开班时间</td>
        <td align="center">{{this.beginTime}}</td>
        <td align="center">结束时间</td>
        <td align="center">{{this.endTime}}</td>
      </tr>
      <tr>
        <td align="center">班级公告</td>
        <td align="center" colspan="3">{{this.notice}}</td>
      </tr>
    </table>
    <div class="mark">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <span style="padding-right:40px">学员信息（{{classListLength||0}}人未分班）（系统自动按照报名类目采用时间顺序生成）</span>
        <el-form-item label="输入搜素">
          <el-input v-model="formInline.user" placeholder="姓名/身份证号"></el-input>
        </el-form-item>
        <el-button size="mini" @click="studentList()">查询</el-button>

        <el-button type="primary" size="mini" @click="addStudent">添加学员</el-button>
        <el-button type="success" size="mini" @click="getNoticeStudentList">批量通知</el-button>
        <el-button type="success" size="mini" @click="excel">批量导出</el-button>
        <el-button type="success" size="mini" @click="excelImage">导出图片</el-button>
      </el-form>
    </div>
    <el-dialog title="添加学员" :visible.sync="dialogFormVisible">
      <el-table :data="gridData" @selection-change="handleSelectionChange">
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column property="pcode" label="学员编号" width="150"></el-table-column>
        <el-table-column property="creationTime" label="报名时间" width="200"></el-table-column>
        <el-table-column property="phoneNumber" label="手机号"></el-table-column>
        <el-table-column property="pname" label="学员姓名"></el-table-column>
        <el-table-column property="trainingType" label="报名项目"></el-table-column>
      </el-table>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addStu">确 定</el-button>
      </div>
    </el-dialog>
    <el-table
      :data="classList"
      @selection-change="getNoticeStudent"
      border
      v-loading="dataListLoading"
      style="width: 100%"
    >
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column prop="pcode" header-align="center" align="center" label="学员编号"></el-table-column>
      <el-table-column prop="creationTime" header-align="center" align="center" label="报名时间"></el-table-column>
      <el-table-column prop="phoneNumber" header-align="center" align="center" label="学员手机号"></el-table-column>
      <el-table-column prop="pname" header-align="center" align="center" label="学员姓名"></el-table-column>
      <el-table-column
        prop="trainingCategoryName"
        header-align="center"
        align="center"
        label="职业技能培训"
      ></el-table-column>
      <el-table-column prop="affiliatedUnit" header-align="center" align="center" label="所属企业"></el-table-column>
      <el-table-column prop="trainingTypeName" header-align="center" align="center" label="培训职业工种"></el-table-column>
      <el-table-column prop="wechatNumber" header-align="center" align="center" label="微信号"></el-table-column>
      <el-table-column prop="trainerClassStatus" header-align="center" align="center" label="学员状态"></el-table-column>
      <el-table-column
        prop="notice"
        header-align="center"
        align="center"
        :formatter="StudentStatus"
        label="开班通知"
      ></el-table-column>
      <el-table-column prop="status" header-align="center" align="center" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="delestudents(scope.row.fid)">移出</el-button>
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
</template>

<script>
export default {
  data() {
    return {
      formInline: {
        user: "",
        region: "",
      },
      dataNames: "",
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dialogFormVisible: false,
      dataList: {},
      datatable: [],
      name: "",
      master: "",
      days: "",
      classNum: "",
      phoneNumber: "",
      createTime: "",
      beginTime: "",
      endTime: "",
      notice: "",
      status: "",
      type: "",
      remark: "",
      idClass: "",
      addStulist: [],
      classListLength: "",
      addStulist: [],
      gridData: [],
      classList: [],
      multipleSelection: [],
      NoticeStudent: [],
      NoticeStudentlist: [],
    };
  },
  created() {
    this.oneValues();
    this.TrainStudentList();
  },
  methods: {
    // 每页数
    sizeChangeHandle(val) {
      console.log(1);

      this.pageSize = val;
      this.pageIndex = 1;
      this.studentList();
    },
    // 当前页
    currentChangeHandle(val) {
      console.log(2);
      this.pageIndex = val;
      this.studentList();
    },
    onSubmit() {
      console.log("submit!");
    },

    // 当前页

    //删除学员
    delestudents(e) {
      console.log(e);
      var list = [];
      list.push(e);
      this.$http({
        url: this.$http.adornUrl("/class/deleteStudent"),
        method: "post",
        data: this.$http.adornData({
          id: this.idClass,
          studentList: list,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "恭喜你，移出成功",
            type: "success",
          });
          this.studentList();
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    formatRole: function (row, column) {
      if (row.personneStatus === "1") {
        return "已通过";
      } else if (row.personneStatus === "0") {
        return "未通过";
      } else {
        return "待审核";
      }
    },
    StudentStatus(row, column) {
      if (row.notice === 1) {
        return "已通知";
      } else {
        return "未通知";
      }
    },
    studentList() {
      this.$http({
        url: this.$http.adornUrl("/class/trainer/list"),
        method: "get",
        params: this.$http.adornParams({
          id: this.idClass,
          name: this.formInline.user,
          page: this.pageIndex,
          limit: this.pageSize,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.classList = data.page.list;
          this.totalPage = data.page.totalCount;
        }
      });
    },
    //统计未分班人数
    TrainStudentList() {
      console.log(this.idType);
      this.$http({
        url: this.$http.adornUrl("/class/trainerCount"),
        method: "get",
        params: this.$http.adornParams({
          type: this.type,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          // this.classList = data.list;
          this.classListLength = data.count;
          // console.log(this.classListLength);
        }
      });
    },

    //添加学元
    addStudent() {
      console.log(this.type);
      this.dialogFormVisible = true;
      this.$http({
        url: this.$http.adornUrl("/sys/getTrainingPersonByTypeID"),
        method: "get",
        params: this.$http.adornParams({
          trainingTypeID: this.type,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.gridData = data.list;
        }
      });
    },
    //导出图片
    excelImage() {
      let a = document.createElement("a");
      a.href =
        "http://192.168.1.112:8070/vocational-training/sys/file/trainerPictureExport?id=" +
        this.idClass;
      a.click();

      // let params = {};
      // this.$http({
      //   url: this.$http.adornUrl("/sys/file/trainerPictureExport"),
      //   method: "get",
      //   responseType: "blob",
      //   params: this.$http.adornParams({
      //     id: this.idClass,
      //   }),
      // })
      //   .then((res) => {
      //     const blob = new Blob([res.data], {
      //       type: "	application/zip",
      //     });
      //     const fileName = "班级人员表.zip";
      //     const linkNode = document.createElement("a");
      //     linkNode.download = fileName; //a标签的download属性规定下载文件的名称
      //     linkNode.style.display = "none";
      //     linkNode.href = URL.createObjectURL(blob); //生成一个Blob URL
      //     document.body.appendChild(linkNode);
      //     linkNode.click(); //模拟在按钮上的一次鼠标单击
      //     URL.revokeObjectURL(linkNode.href); // 释放URL 对象
      //     document.body.removeChild(linkNode);
      //   })
      //   .catch((error) => {
      //     if (error.error)
      //       this.$message.error(
      //         error.error.text ? error.error.text : error.error
      //       );
      //   });
    },
    // 批量导出
    excel(val) {
      console.log(this.NoticeStudentlist.toString());

      let params = {};
      this.$http({
        url: this.$http.adornUrl("/class/trainerExport"),
        method: "get",
        responseType: "blob",
        params: this.$http.adornParams({
          studentIds: this.NoticeStudentlist.toString(),
          id: this.idClass,
        }),
      })
        .then((res) => {
          const blob = new Blob([res.data], {
            type: "application/vnd.ms-excel",
          });
          const fileName = "班级人员表.xls";
          const linkNode = document.createElement("a");
          linkNode.download = fileName; //a标签的download属性规定下载文件的名称
          linkNode.style.display = "none";
          linkNode.href = URL.createObjectURL(blob); //生成一个Blob URL
          document.body.appendChild(linkNode);
          linkNode.click(); //模拟在按钮上的一次鼠标单击
          URL.revokeObjectURL(linkNode.href); // 释放URL 对象
          document.body.removeChild(linkNode);
        })
        .catch((error) => {
          if (error.error)
            this.$message.error(
              error.error.text ? error.error.text : error.error
            );
        });
    },
    // 添加班级学员接口
    addStu() {
      this.dialogFormVisible = false;
      this.$http({
        url: this.$http.adornUrl("/class/saveTrainer"),
        method: "post",
        data: this.$http.adornData({
          studentList: this.addStulist,
          id: this.idClass,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "恭喜你，添加成功",
            type: "success",
          });
          this.studentList();
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    //批量通知
    getNoticeStudent(val) {
      this.NoticeStudent = val;
      console.log(this.NoticeStudent);
      this.NoticeStudentlist = [];
      this.NoticeStudent.map((item) => {
        this.NoticeStudentlist.push(item.fid);
      });
      console.log(this.multipleSelection);
      console.log(this.NoticeStudentlist);
    },
    //批量通知接口传参
    getNoticeStudentList() {
      console.log(this.NoticeStudentlist.length);

      if (!this.NoticeStudentlist.length) {
        return this.$message.error("错了哦，请选择通知学员");
      }
      this.$http({
        url: this.$http.adornUrl("/class/noticeStudent"),
        method: "post",
        data: this.$http.adornData({
          studentList: this.NoticeStudentlist,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "恭喜你，通知成功",
            type: "success",
          });
          this.studentList();
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
      this.addStulist = [];
      this.multipleSelection.map((item) => {
        this.addStulist.push(item.fid);
      });
      console.log(this.multipleSelection);
      console.log(this.addStulist);
    },
    peopleInfo(pid) {
      this.$http({
        url: this.$http.adornUrl("/class/info"),
        method: "get",
        params: this.$http.adornParams({
          id: pid,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.classVO;
          this.name = this.dataList.name;
          this.master = this.dataList.master;
          this.days = this.dataList.days;
          this.classNum = this.dataList.classNum;
          this.phoneNumber = this.dataList.phoneNumber;
          this.createTime = this.dataList.createTime;
          this.beginTime = this.dataList.beginTime;
          this.endTime = this.dataList.endTime;
          this.notice = this.dataList.notice;
          this.status = this.dataList.status;
          this.type = this.dataList.type;
          this.remark = this.dataList.remark;
          this.idClass = this.dataList.id;
          this.dataNames = data.dataDictionaryEntity.dataName;
          console.log(this.type);
          this.studentList();
        }
      });
    },

    oneValues() {
      let result;
      const url = window.location.href;
      console.log(url);
      if (url.indexOf("?") !== -1) {
        result = url.substr(url.indexOf("=") + 1);
        this.fid = result;
        this.peopleInfo(this.fid);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
table {
  width: 100%;
}
tr:last-of-type {
  height: 150px;
}
tr {
  border: solid 1px #a59e9e;
  height: 50px;
}
td {
  border: solid 1px #a59e9e;
  width: 150px;
}
.mark {
  font-size: 16px;
  padding-top: 15px;
  box-sizing: border-box;
  background: floralwhite;
  margin-top: 30px;
}
</style>
