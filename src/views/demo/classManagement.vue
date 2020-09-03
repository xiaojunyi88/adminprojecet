<template>
  <div>
    <div class="btns">
      <div class="btn-left">
        <el-button type="primary" size="mini" @click="gotoMenu" plain>创建班级</el-button>
      </div>
      <div class="btn-right">
        <el-button type="success" plain @click="getDataList()">全部班级（{{classSum}}）</el-button>
        <el-button type="info" plain @click="getbeginSum('1')">正在上课（{{beginSum}}）</el-button>
        <el-button type="warning" plain @click="getendSum('2')">已结束（{{endSum}}）</el-button>
        <el-button type="danger" plain @click="getnotBeginSum('0')">未开始（{{notBeginSum}}）</el-button>
      </div>
    </div>
    <div>
      <el-form
        :inline="true"
        :model="formInline"
        class="demo-form-inline"
        label-width="100px"
        @keyup.enter.native="getDataList"
      >
        <el-form-item label="班级名称">
          <el-input v-model="formInline.user" placeholder="班级名称"></el-input>
        </el-form-item>
        <el-form-item label="创建时间">
          <el-date-picker
            v-model="formInline.value1"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            value-format="yyyy-MM-dd"
          ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">查询</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
      <el-table-column prop="id" header-align="center" align="center" label="班级编号"></el-table-column>

      <el-table-column prop="name" header-align="center" align="center" label="班级名称">
        <template slot-scope="{row}">
          <router-link :to="'/classInfo?id='+row.id" class="link-type">
            <span>{{row.name}}</span>
          </router-link>
        </template>
      </el-table-column>
      <el-table-column prop="createTime" header-align="center" align="center" label="创建时间"></el-table-column>
      <el-table-column prop="beginTime" header-align="center" align="center" label="开班时间"></el-table-column>
      <el-table-column prop="endTime" header-align="center" align="center" label="结束时间"></el-table-column>
      <el-table-column prop="days" header-align="center" align="center" label="学习周期"></el-table-column>
      <el-table-column prop="classStudentSum" header-align="center" align="center" label="实际班级人数"></el-table-column>
      <el-table-column prop="master" header-align="center" align="center" label="班主任"></el-table-column>
      <el-table-column prop="notice" header-align="center" align="center" label="班级公告"></el-table-column>
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
        value1: ""
      },
      dataList: [],
      endSum: "",
      beginSum: "",
      classSum: "",
      notBeginSum: "",
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      beginTime: []
    };
  },
  created() {
    this.getDataList();
  },
  methods: {
    onSubmit() {
      console.log(this.formInline.value1);
      this.$http({
        url: this.$http.adornUrl("/class/list"),
        method: "get",
        params: this.$http.adornParams({
          beginTime: this.formInline.value1[0],
          endTime: this.formInline.value1[1]
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
        }
      });
    },
    gotoMenu() {
      this.$router.replace({ name: "addClass" });
    },
    //喂开始
    getnotBeginSum(state) {
      this.$http({
        url: this.$http.adornUrl("/class/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.formInline.user,
          status: state,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.notBeginSum = data.notBeginSum;
          this.beginSum = data.beginSum;
          this.classSum = data.classSum;
          this.endSum = data.endSum;
          this.totalPage = data.page.totalCount;
        }
      });
    },
    //已结束
    getendSum(state) {
      this.$http({
        url: this.$http.adornUrl("/class/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.formInline.user,
          status: state,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.notBeginSum = data.notBeginSum;
          this.beginSum = data.beginSum;
          this.classSum = data.classSum;
          this.endSum = data.endSum;
          this.totalPage = data.page.totalCount;
        }
      });
    },
    //进行中
    getbeginSum(state) {
      this.$http({
        url: this.$http.adornUrl("/class/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.formInline.user,
          status: state,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.notBeginSum = data.notBeginSum;
          this.beginSum = data.beginSum;
          this.classSum = data.classSum;
          this.endSum = data.endSum;
          this.totalPage = data.page.totalCount;
        }
      });
    },
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/class/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.formInline.user,
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.notBeginSum = data.notBeginSum;
          this.beginSum = data.beginSum;
          this.classSum = data.classSum;
          this.endSum = data.endSum;
          this.totalPage = data.page.totalCount;
        }
      });
      this.dataListLoading = false;
    },

    // 导出
    ecxl() {
      let params = {};
      console.log(this.formInline.value1);

      this.$http({
        url: this.$http.adornUrl("/class/export"),
        method: "get",
        responseType: "blob",
        params: this.$http.adornParams({
          beginTime: this.formInline.value1,
          endTime: this.formInline.value1[1]
        })
      })
        .then(res => {
          const blob = new Blob([res.data], {
            type: "application/vnd.ms-excel"
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
        .catch(error => {
          if (error.error)
            this.$message.error(
              error.error.text ? error.error.text : error.error
            );
        });
    },
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
.btns {
  display: flex;
  margin-bottom: 20px;
  > .btn-left {
    width: 300px;
  }
  > .btn-right {
    flex: 1;
  }
}
</style>
