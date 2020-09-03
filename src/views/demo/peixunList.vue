<template>
  <div>
    <div>
      <el-button @click="getList" type="success">全部人员（{{this.totalPage}}）</el-button>
    </div>
    <div class="inquire-box">
      <el-form ref="form" :inline="true" :model="form" label-width="5.5vw">
        <el-row type="flex">
          <el-col :span="6">
            <div class="grid-content bg-purple">
              <el-row>
                <el-col :span="20">
                  <div>
                    <el-form-item label="输入搜索">
                      <el-input v-model="form.name" style="width:10vw" placeholder="姓名/身份证"></el-input>
                    </el-form-item>
                  </div>
                </el-col>
                <el-col :span="4">
                  <div>
                    <el-button type="primary" size="mini" @click="onSubmit">查询</el-button>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="9" :offset="1">
            <div class="grid-content bg-purple-light">
              <el-row>
                <el-col :span="18">
                  <div class="grid-content bg-purple">
                    <el-form-item label="时间筛选">
                      <el-date-picker
                        v-model="form.value1"
                        type="daterange"
                        range-separator="至"
                        start-placeholder="开始日期"
                        end-placeholder="结束日期"
                        value-format="yyyy-MM-dd"
                        style="width:16vw"
                      ></el-date-picker>
                      <!-- <el-date-picker
                        v-model="form.value1"
                        type="daterange"
                        range-separator="至"
                        start-placeholder="开始日期"
                        end-placeholder="结束日期"
                        value-format="yyyy-MM-dd"
                        style="width:16vw"
                      ></el-date-picker>-->
                    </el-form-item>
                  </div>
                </el-col>
                <el-col :span="3">
                  <div class="grid-content bg-purple-light">
                    <el-button type="primary" size="mini" @click="getTime">查询</el-button>
                  </div>
                </el-col>
                <el-col :span="3">
                  <div class="grid-content bg-purple">
                    <el-button type="success" size="mini" @click="clearInfo">清空</el-button>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="4">
            <div class="grid-content bg-purple">
              <el-row>
                <el-col :span="24">
                  <div class="grid-content bg-purple-dark">
                    <el-form-item label="人员类别">
                      <el-select
                        v-model="form.region"
                        placeholder="请选择人员类别"
                        style="width:7vw"
                        @change="getTrainingCategory(form.region)"
                      >
                        <el-option
                          :label="item.dataName"
                          :value="item.fid"
                          v-for="(item,index) in this.getPersonnelCategoryList"
                          :key="item+index"
                        ></el-option>
                      </el-select>
                    </el-form-item>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="4">
            <div class="grid-content bg-purple-light">
              <el-row>
                <el-col :span="24">
                  <div class="grid-content bg-purple-dark">
                    <el-form-item label="职业培训">
                      <el-select
                        v-model="form.TrainingCategory"
                        placeholder="请选择职业培训"
                        style="width:7vw"
                        @change="getTraining(form.TrainingCategory)"
                      >
                        <el-option
                          :label="item.dataName"
                          :value="item.fid"
                          v-for="(item,index) in  this.getTrainingCategoryList"
                          :key="item+index"
                        ></el-option>
                      </el-select>
                    </el-form-item>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="7">
            <div class="grid-content bg-purple">
              <el-row>
                <el-col :span="18">
                  <div>
                    <el-form-item label="班级列表">
                      <el-select v-model="form.className" placeholder="请选择班级" style="width:10vw">
                        <el-option
                          :label="item.name"
                          :value="item.id"
                          v-for="(item,index) in this.classLists"
                          :key="item+index"
                        ></el-option>
                      </el-select>
                    </el-form-item>
                  </div>
                </el-col>
                <el-col :span="6">
                  <div>
                    <el-button type="primary" size="mini" @click="getClassLists(form.className)">查询</el-button>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="17">
            <div class="grid-content bg-purple">
              <el-row>
                <el-col :span="24">
                  <div>
                    <el-button type="success" @click="ecxl()" style="float:right">导出表格</el-button>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
        </el-row>
      </el-form>
    </div>

    <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
      <el-table-column prop="pcode" header-align="center" align="center" label="编号"></el-table-column>
      <el-table-column prop="pname" header-align="center" align="center" label="姓名">
        <template slot-scope="{row}">
          <router-link :to="'/peopleInfo?fid='+row.fid" class="link-type">
            <span>{{ row.pname }}</span>
          </router-link>
        </template>
      </el-table-column>
      <el-table-column prop="pgender" header-align="center" align="center" label="性别"></el-table-column>
      <el-table-column prop="idNumber" header-align="center" align="center" label="身份证号"></el-table-column>
      <el-table-column prop="phoneNumber" header-align="center" align="center" label="电话"></el-table-column>
      <el-table-column prop="personnelCategory" header-align="center" align="center" label="人员类别"></el-table-column>
      <el-table-column prop="trainingCategory" header-align="center" align="center" label="职业技能培训"></el-table-column>
      <el-table-column prop="creationTime" header-align="center" align="center" label="报名时间"></el-table-column>
      <el-table-column prop="trainingType" header-align="center" align="center" label="培训职业（工种）"></el-table-column>
      <el-table-column prop="trainingLevel" header-align="center" align="center" label="培训等级"></el-table-column>
      <el-table-column prop="wechatNumber" header-align="center" align="center" label="微信号"></el-table-column>

      <el-table-column prop="status" header-align="center" align="center" label="身份证照">
        <template slot-scope="{row}">
          <router-link :to="{name:'peopleInfo',query:{fid:row.fid}}" class="link-type">
            <span>查看</span>
          </router-link>
        </template>
      </el-table-column>

      <el-table-column prop="affiliatedUnit" header-align="center" align="center" label="所属单位"></el-table-column>
      <el-table-column prop="className" header-align="center" align="center" label="班级名称"></el-table-column>
      <el-table-column prop="state" header-align="center" align="center" label="状态" fixed="right"></el-table-column>
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
      form: {
        name: "",
        className: "",
        region: "",
        value1: "",
        TrainingCategory: "",
      },
      classLists: [],
      dataList: [],
      getPersonnelCategoryList: [], // 人员类别
      getTrainingCategoryList: [], //职业技能培训
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      classId: "",
      dataListLoading: false,
    };
  },
  created() {
    this.getList();
    this.getPersonnelCategorys();
    this.getclassList();
  },
  methods: {
    //获取班级列表
    getclassList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/class/classlist"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.classLists = data.classEntityList;
        }
      });
      this.dataListLoading = false;
    },
    getTraining(e) {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/trainerListQuery"),
        method: "get",
        params: this.$http.adornParams({
          keyword: this.form.name,
          idNumber: "",
          startDate: "",
          endDate: "",
          personnelCategory: this.form.region,
          trainingCategory: this.form.TrainingCategory,
          page: this.pageIndex,
          limit: this.pageSize,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    getList(e) {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/trainerListQuery"),
        method: "get",
        params: this.$http.adornParams({
          keyword: this.form.name,
          idNumber: "",
          startDate: "",
          endDate: "",
          personnelCategory: this.form.region,
          trainingCategory: this.form.TrainingCategory,
          page: this.pageIndex,
          limit: this.pageSize,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    //导出表格
    ecxl() {
      let params = {};

      this.$http({
        url: this.$http.adornUrl("/sys/trainerExport"),
        method: "get",
        responseType: "blob",
        params: this.$http.adornParams({
          classId: this.classId,
        }),
      })
        .then((res) => {
          const blob = new Blob([res.data], {
            type: "application/vnd.ms-excel",
          });
          const fileName = "培训人员表.xls";
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
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getList();
    },
    //班级查询
    getClassLists(e) {
      this.classId = e;
      this.$http({
        url: this.$http.adornUrl("/sys/trainerListQuery"),
        method: "get",
        params: this.$http.adornParams({
          keyword: this.form.name,
          classId: this.classId,
          startDate: "",
          endDate: "",
          personnelCategory: "",
          trainingCategory: "",
          page: this.pageIndex,
          limit: this.pageSize,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          this.pageIndex = data.page.totalPage;
          this.pageIndex = 1;
          this.pageSize = 10;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 时间查询
    getTime() {
      this.form.name = "";
      console.log(this.form.value1.slice(0));
      this.$http({
        url: this.$http.adornUrl("/sys/trainerListQuery"),
        method: "get",
        params: this.$http.adornParams({
          pname: "",
          idNumber: "",
          startDate: this.form.value1[0],
          endDate: this.form.value1[1],
          personnelCategory: this.form.TrainingCategory,
          trainingCategory: "",
          page: this.pageIndex,
          limit: this.pageSize,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          this.pageIndex = data.page.totalPage;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 清空信息
    clearInfo() {
      this.form.name = "";
      this.form.value1 = "";
      this.form.region = "";
      this.getList();
    },
    onSubmit() {
      console.log(this.form.value1);
      this.$http({
        url: this.$http.adornUrl("/sys/trainerListQuery"),
        method: "get",
        params: this.$http.adornParams({
          keyword: this.form.name,
          idNumber: "",
          startDate: "",
          endDate: "",
          personnelCategory: "",
          trainingCategory: "",
          page: this.pageIndex,
          limit: this.pageSize,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          this.pageIndex = data.page.totalPage;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 获取人员类别
    getPersonnelCategorys() {
      this.$http({
        url: this.$http.adornUrl("/app/getPersonnelCategory"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.getPersonnelCategoryList = data.list;

          console.log(this.getPersonnelCategoryList);
        }
      });
    },
    // 获取职业培训
    getTrainingCategory(e) {
      console.log(e);
      this.getTrainingCategoryList = "";
      this.form.TrainingCategory = "";
      this.$http({
        url: this.$http.adornUrl("/app/getTrainingCategory"),
        method: "get",
        params: this.$http.adornParams({
          personCategoryID: e,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.getTrainingCategoryList = data.list;
        }
      });
      this.getList();
    },
  },
};
</script>

<style lang="scss" scoped>
.el-row {
  margin-top: 20px;
  width: 100%;
  &:last-child {
    margin-bottom: 0;
    margin-top: 0px;
  }
}
.contain {
  object-fit: contain;
  width: 50px;
}
</style>
