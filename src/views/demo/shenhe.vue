<template>
  <div>
    <div class="shenhe">
      <el-button type="primary" @click=" getSuccess(1)">已审核（{{reviewed}}）</el-button>
      <el-button type="success" @click=" getPrimary(2)">待审核（{{toBeReviewed}}）</el-button>
      <el-button type="info" @click=" getInfo(0)">未通过（{{fail}}）</el-button>
    </div>
    <div style="margin-top:20px">
      <el-form ref="form" :model="sizeForm" :inline="true" label-width="80px" size="mini">
        <el-form-item label="输入搜索">
          <el-input v-model="sizeForm.name" placeholder="姓名/身份证号"></el-input>
        </el-form-item>
        <el-button type="success" size="mini" @click="nameIdcard">搜索</el-button>
        <el-form-item label="培训类别">
          <el-select
            v-model="sizeForm.region"
            placeholder="请选择培训类别"
            @change="getTrainingCategoryNum"
          >
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.TrainingCatList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
      <el-table-column prop="pcode" header-align="center" align="center" label="编号"></el-table-column>
      <el-table-column prop="persionNum" header-align="center" align="center" label="照片">
        <template slot-scope="{row}">
          <router-link :to="{name:'shenheInfo',query:{fid:row.fid}}" class="link-type">
            <span>查看</span>
          </router-link>
        </template>
      </el-table-column>
      <el-table-column prop="pname" header-align="center" align="center" label="姓名">
        <template slot-scope="{row}">
          <router-link :to="'/shenheInfo?fid='+row.fid" class="link-type">
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
      <!-- <el-table-column prop="status" header-align="center" align="center" label="身份证照"></el-table-column> -->
      <el-table-column prop="affiliatedUnit" header-align="center" align="center" label="所属单位"></el-table-column>
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
      sizeForm: {
        name: "",
        region: ""
      },
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      fail: "",
      toBeReviewed: "",
      reviewed: "",
      dataListLoading: false,
      dataList: [],
      TrainingCatList: [],
      num: null,
      dataListSuccess: []
    };
  },
  created() {
    this.getDataList();
    this.getTrainingCategory();
    this.getPersonNumberByState();
  },
  methods: {
    // 获取培训类别
    getTrainingCategory() {
      this.$http({
        url: this.$http.adornUrl("/sys/getTrainingCat"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.TrainingCatList = data.list;
        }
      });
    },
    // 根据培训类别筛选
    getTrainingCategoryNum() {
      this.dataListLoading = true;
      this.sizeForm.name = "";
      this.$http({
        url: this.$http.adornUrl("/sys/queryTrainerExamineList"),
        method: "post",
        data: this.$http.adornData({
          pname: "",
          idNumber: "",
          trainingCategory: this.sizeForm.region,
          page: this.pageIndex,
          limit: this.pageSize
        })
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
    // 根据姓名，id删选
    nameIdcard() {
      this.dataListLoading = true;
      this.sizeForm.region = "";
      this.$http({
        url: this.$http.adornUrl("/sys/queryTrainerExamineList"),
        method: "post",
        data: this.$http.adornData({
          keyword: this.sizeForm.name,
          idNumber: "",
          trainingCategory: "",
          page: this.pageIndex,
          limit: this.pageSize
        })
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
    //审核人数统计
    getPersonNumberByState() {
      this.$http({
        url: this.$http.adornUrl("/sys/getPersonNumberByState"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.fail = data.fail;
          this.reviewed = data.reviewed;
          this.toBeReviewed = data.toBeReviewed;
        }
      });
    },
    getPrimary(num) {
      this.$http({
        url: this.$http.adornUrl("/sys/queryTrainerExamineList"),
        method: "post",
        data: this.$http.adornData({
          pname: this.sizeForm.name,
          idNumber: "",
          trainingCategory: "",
          personneStatus: num,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          this.pageIndex = 1;
          this.pageSize = 10;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    getSuccess(num) {
      this.pageIndex = num;
      this.$http({
        url: this.$http.adornUrl("/sys/queryTrainerExamineList"),
        method: "post",
        data: this.$http.adornData({
          pname: this.sizeForm.name,
          idNumber: "",
          trainingCategory: "",
          personneStatus: 1,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataListSuccess = data.page.list;
          this.totalPage = data.page.totalCount;
          this.dataList = this.dataListSuccess;
          this.pageIndex = data.page.currPage;
          this.pageSize = 10;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    getInfo(num) {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/queryTrainerExamineList"),
        method: "post",
        data: this.$http.adornData({
          pname: this.sizeForm.name,
          idNumber: "",
          trainingCategory: "",
          personneStatus: num,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          this.pageIndex = 1;
          this.pageSize = 10;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    getDataList() {
      console.log(this.num);
      this.dataListSuccess = [];
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/queryTrainerExamineList"),
        method: "post",
        data: this.$http.adornData({
          pname: this.sizeForm.name,
          personneStatus: this.num,
          idNumber: "",
          trainingCategory: "",
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          // this.pageIndex = 1;
          // this.pageSize = 10;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      console.log(val, 11111111111111);

      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      console.log(val);
      this.pageIndex = val;
      this.getDataList();

      // this.getDataList();
    }
  }
};
</script>

<style lang="scss" scoped>
.shenhe {
  background: #eee;
  height: 60px;
  padding-top: 12px;
  padding-left: 12px;
}
</style>
