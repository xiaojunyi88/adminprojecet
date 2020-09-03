<template>
  <div>
    <el-card shadow="always">
      <el-form :inline="true" :model="formInline" class="demo-form-inline" label-width="100px">
        <el-row>
          <el-col :span="24">
            <div class="grid-content bg-purple-dark">
              <el-form-item label="班级名称">
                <el-input v-model="formInline.name" placeholder="班级名称"></el-input>
              </el-form-item>
            </div>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12">
            <div class="grid-content bg-purple">
              <el-form-item label="学习周期">
                <el-select v-model="formInline.days" placeholder="请选择">
                  <el-option label="15" value="15"></el-option>
                </el-select>
              </el-form-item>
            </div>
          </el-col>
          <el-col :span="12">
            <div class="grid-content bg-purple-light">
              <el-form-item label="班主任名">
                <el-input v-model="formInline.master" placeholder="班主任姓名"></el-input>
              </el-form-item>
            </div>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12">
            <div class="grid-content bg-purple">
              <el-form-item label="学员数量">
                <el-select v-model="formInline.classNum" placeholder="请选择">
                  <el-option label="60" value="60"></el-option>
                </el-select>
              </el-form-item>
            </div>
          </el-col>
          <el-col :span="12">
            <div class="grid-content bg-purple-light">
              <el-form-item label="备注">
                <el-input v-model="formInline.remark" placeholder="备注"></el-input>
              </el-form-item>
            </div>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12">
            <div class="grid-content bg-purple">
              <el-form-item label="培训种类">
                <el-select
                  v-model="formInline.type"
                  placeholder="请选择培训种类"
                  @change="TrainStudentList(formInline.type)"
                >
                  <el-option
                    :label="item.dataName"
                    :value="item.fid"
                    v-for="(item,index) in  this.TrainingType"
                    :key="item+index"
                  ></el-option>
                </el-select>
              </el-form-item>
            </div>
          </el-col>
          <el-col :span="12">
            <div class="grid-content bg-purple-light"></div>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12">
            <div class="grid-content bg-purple">
              <el-form-item label="开班时间">
                <el-date-picker
                  v-model="formInline.beginTime"
                  type="date"
                  placeholder="选择日期"
                  value-format="yyyy-MM-dd"
                ></el-date-picker>
              </el-form-item>
            </div>
          </el-col>
          <el-col :span="12">
            <div class="grid-content bg-purple-light">
              <el-form-item label="结束时间">
                <el-date-picker
                  v-model="formInline.endTime"
                  type="date"
                  placeholder="选择日期"
                  value-format="yyyy-MM-dd"
                ></el-date-picker>
              </el-form-item>
            </div>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12">
            <div class="grid-content bg-purple">
              <el-form-item label="班级公告">
                <el-input type="textarea" :rows="5" v-model="formInline.notice" style="width:30vw"></el-input>
              </el-form-item>
            </div>
          </el-col>
          <el-col :span="12">
            <div class="grid-content bg-purple-light"></div>
          </el-col>
        </el-row>
        <div style="padding-left:30vw">
          <el-button type="success" @click="onSubmit()" plain>提交</el-button>
        </div>
      </el-form>
    </el-card>

    <div class="mark">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <span
          style="padding-right:40px"
        >学员信息（{{ this.classListLength||'正在计算'}}人未分班）（系统自动按照报名类目采用时间顺序生成）</span>
        <el-form-item label="审批人">
          <el-input v-model="formInline.user" placeholder="审批人"></el-input>
        </el-form-item>
        <el-button size="mini" @click="studentList">查询</el-button>

        <el-button type="primary" size="mini" @click="addStudent">添加学员</el-button>
        <el-button type="success" size="mini" @click="getNoticeStudentList()">批量通知</el-button>
        <el-button type="success" size="mini" @click="excel()">批量导出</el-button>
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
      border
      v-loading="dataListLoading"
      style="width: 100%"
      @selection-change="getNoticeStudent"
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
        name: "",
        beginTime: "",
        endTime: "",
        days: "",
        classNum: "",
        master: "",
        notice: "",
        status: "",
        remark: "",
        type: ""
      },
      TrainingType: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      classId: "",
      dataList: [],
      classList: [],
      dataListLoading: false,
      dialogFormVisible: false,
      classListLength: "",
      formLabelWidth: "120px",
      gridData: [],
      multipleSelection: [],
      NoticeStudent: [],
      NoticeStudentlist: [],
      idType: null,
      addStulist: []
    };
  },
  created() {
    this.getTrainingType();
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
    formatRole: function(row, column) {
      if (row.personneStatus === 1) {
        return "已通过";
      } else if (row.personneStatus === "0") {
        return "未通过";
      } else {
        return "待审核";
      }
    },
    StudentStatus(row, column) {
      if (row.notice === "1") {
        return "已通知";
      } else {
        return "未通知";
      }
    },
    // 获取培训工种
    getTrainingType() {
      this.$http({
        url: this.$http.adornUrl("/app/getSysTrainingType"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.TrainingType = data.list;
        }
      });
    },
    //删除学员
    delestudents(e) {
      console.log(e);
      var list = [];
      list.push(e);
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/class/deleteStudent"),
        method: "post",
        data: this.$http.adornData({
          id: this.classId,
          studentList: list
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.studentList();
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    //添加学员
    addStudent() {
      console.log(this.idType);
      this.dialogFormVisible = true;
      this.$http({
        url: this.$http.adornUrl("/sys/getTrainingPersonByTypeID"),
        method: "get",
        params: this.$http.adornParams({
          trainingTypeID: this.idType
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          // this.classList = data.list;
          this.gridData = data.list;
          // console.log(this.classListLength);
        }
      });
    },
    onSubmit() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/class/save"),
        method: "post",
        data: this.$http.adornData({
          name: this.formInline.name,
          beginTime: this.formInline.beginTime,
          endTime: this.formInline.endTime,
          days: this.formInline.days,
          classNum: this.formInline.classNum,
          master: this.formInline.master,
          notice: this.formInline.notice,
          type: this.formInline.type,
          remark: this.formInline.remark
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.classId = data.id;
          this.$message({
            message: "恭喜你，新建班级成功",
            type: "success"
          });
          this.formInline.name = "";
          this.formInline.beginTime = "";
          this.formInline.endTime = "";
          this.formInline.days = "";
          this.formInline.classNum = "";
          this.formInline.master = "";
          this.formInline.notice = "";
          this.formInline.type = "";
          this.formInline.remark = "";
          if (this.classId) {
            this.studentList();
          }
        } else {
          this.$message.error("错了哦，请重新填写信息");
          this.formInline.name = "";
          this.formInline.beginTime = "";
          this.formInline.endTime = "";
          this.formInline.days = "";
          this.formInline.classNum = "";
          this.formInline.master = "";
          this.formInline.notice = "";
          this.formInline.type = "";
          this.formInline.remark = "";
        }
        this.dataListLoading = false;
      });
    },
    //获取未分班
    TrainStudentList(id) {
      this.idType = id;
      console.log(this.idType);
      this.$http({
        url: this.$http.adornUrl("/class/trainerCount"),
        method: "get",
        params: this.$http.adornParams({
          type: id
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          // this.classList = data.list;
          this.classListLength = data.count;

          // console.log(this.classListLength);
        }
      });
    },

    studentList() {
      this.$http({
        url: this.$http.adornUrl("/class/trainer/list"),
        method: "get",
        params: this.$http.adornParams({
          id: this.classId,
          name: this.formInline.user,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.classList = data.page.list;
          this.totalPage = data.page.totalCount;
        }
      });
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
      this.addStulist = [];
      this.multipleSelection.map(item => {
        this.addStulist.push(item.fid);
      });
      console.log(this.multipleSelection);
      console.log(this.addStulist);
    },
    // 添加班级学员接口
    addStu() {
      this.dataListLoading = true;
      this.dialogFormVisible = false;
      this.$http({
        url: this.$http.adornUrl("/class/saveTrainer"),
        method: "post",
        data: this.$http.adornData({
          studentList: this.addStulist,
          id: this.classId
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "恭喜你，添加成功",
            type: "success"
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
      this.NoticeStudent.map(item => {
        this.NoticeStudentlist.push(item.fid);
      });
      console.log(this.multipleSelection);
      console.log(this.NoticeStudentlist);
    },
    // 批量导出
    excel(val) {
      console.log(this.NoticeStudentlist);
      let params = {};
      this.$http({
        url: this.$http.adornUrl("/class/trainerExport"),
        method: "get",
        responseType: "blob",
        params: this.$http.adornParams({
          studentList: this.NoticeStudentlist,
          id: this.classId
        })
      })
        .then(res => {
          const blob = new Blob([res.data], {
            type: "application/vnd.ms-excel"
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
        .catch(error => {
          if (error.error)
            this.$message.error(
              error.error.text ? error.error.text : error.error
            );
        });
    },
    //批量通知接口传参
    getNoticeStudentList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/class/noticeStudent"),
        method: "post",
        data: this.$http.adornData({
          studentList: this.NoticeStudentlist
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "恭喜你，通知成功",
            type: "success"
          });
          this.studentList();
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.mark {
  font-size: 16px;
  padding-top: 15px;
  box-sizing: border-box;
  background: floralwhite;
  margin-top: 30px;
}
</style>
