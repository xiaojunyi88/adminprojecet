<template>
  <div>
    <div>
      <el-button type="text" @click="oneAddmenu">添加一级数据</el-button>
    </div>
    <!-- 一级菜单弹框 -->
    <el-dialog title="添加一级数据" :visible.sync="dialogFormVisible" @close="cleardialogFormVisible">
      <el-form :model="form">
        <el-form-item label="一级数据名称" :label-width="formLabelWidth">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="备注" :label-width="formLabelWidth">
          <el-input v-model="form.region"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cleardialogFormVisible">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisibles">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 数据列表 -->
    <div>
      <el-form ref="form" :model="sizeForm" :inline="true" label-width="80px" size="mini">
        <el-form-item label="输入搜索">
          <el-input v-model="sizeForm.name" placeholder="数据名称"></el-input>
        </el-form-item>
        <el-button type="success" size="mini" @click="getDataList()">搜索</el-button>
      </el-form>
      <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
        <el-table-column prop="dataName" header-align="center" align="center" label="数据名称"></el-table-column>
        <el-table-column prop="classification" header-align="center" align="center" label="数据级别"></el-table-column>
        <el-table-column prop="remarks" header-align="center" align="center" label="备注"></el-table-column>
        <el-table-column prop="creationTime" header-align="center" align="center" label="添加时间"></el-table-column>

        <el-table-column prop="state" header-align="center" align="center" label="是否启用">
          <template slot-scope="scope">
            <el-button
              type="text"
              size="small"
              @click="oneStateList(scope.row.fid,scope.row.state)"
            >{{scope.row.state}}</el-button>

            <!-- <el-button v-if="scope.row.businessStatus === '停用'" type="text" size="small">停用</el-button>
            <el-button v-else type="text" size="small">启用</el-button>-->
          </template>
        </el-table-column>
        <el-table-column prop="pname" header-align="center" align="center" label="操作">
          <template slot-scope="scope">
            <el-button
              type="text"
              size="small"
              @click="addTwoData(scope.row.fid,scope.row.dataName)"
            >添加二级分类</el-button>
            <el-button
              type="text"
              size="small"
              @click="reviseOneList(scope.row.fid,scope.row.dataName,scope.row.remarks)"
            >修改</el-button>
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
    <!-- 二级菜单弹框 -->
    <el-dialog title="添加二级数据" :visible.sync="twoDatadialog">
      <el-form :model="formTwo" :inline="true">
        <el-form-item label="二级数据名称" :label-width="formLabelWidth">
          <el-input v-model="formTwo.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="排序" :label-width="formLabelWidth">
          <el-input v-model="formTwo.region" autocomplete="off" placeholder="请填写数字"></el-input>
        </el-form-item>
        <el-form-item label="一级数据" :label-width="formLabelWidth">
          <el-input v-model="formTwo.oneData" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="其他关联数据" :label-width="formLabelWidth">
          <el-select v-model="formTwo.TrainingCategory" placeholder="请选择关联数据">
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.getTrainingCategoryList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="备注" :label-width="formLabelWidth">
          <el-input type="textarea" :rows="5" v-model="formTwo.remake" style="width:28vw"></el-input>
        </el-form-item>
      </el-form>
      <div style="padding-left:20vw">
        <el-button @click="getTwoDatadialogClear()">取 消</el-button>
        <el-button @click="getTwoDatadialogs()">确 定</el-button>
      </div>
      <!-- 二级数据修改 -->

      <!-- 数据列表 -->
      <div class="TwoList">
        <h3>数据列表</h3>
        <el-table :data="twotableData" height="150" border style="width: 100%">
          <el-table-column prop="dataName" label="数据名称" width="180"></el-table-column>
          <el-table-column prop="classification" label="一级数据" width="180"></el-table-column>
          <el-table-column prop="remarks" label="备注"></el-table-column>
          <el-table-column prop="sort" label="排序"></el-table-column>
          <el-table-column prop="address" label="操作">
            <template slot-scope="scope">
              <el-button
                type="text"
                size="small"
                @click="reviseTwoList(scope.row.fid,scope.row.classification,scope.row.dataName,scope.row.remarks,scope.row.sort)"
              >修改</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="twoDatadialog = false">取 消</el-button>
        <el-button type="primary" @click="twoDatadialog=false">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dialogTableVisible: false,
      reviseTwoVisible: false,
      dialogFormVisible: false,
      twoDatadialog: false,
      formLabelWidth: "120px",
      istwo: false,
      oneStateRe: "",
      onePid: "",
      form: {
        name: "",
        region: ""
      },
      formTwo: {
        name: "",
        region: "",
        oneData: "",
        TrainingCategory: "",
        remake: "",
        oneDataId: ""
      },
      sizeForm: {
        name: ""
      },
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataList: [],
      oneData: "",
      twotableData: [],
      getTrainingCategoryList: []
    };
  },
  created() {
    this.getDataList();
  },
  methods: {
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    //开启1、关闭0 状态
    oneStateList(id, name) {
      var state = 2;
      if (name === "停用") {
        state = 1;
      } else {
        state = 0;
      }
      this.$http({
        url: this.$http.adornUrl("/sys/updateState"),
        method: "get",
        params: this.$http.adornParams({
          fid: id,
          state: state
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.getDataList();
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 一级菜单弹框方法
    dialogFormVisibles() {
      this.dialogFormVisible = false;
      if (this.oneStateRe === "修改一级") {
        this.$http({
          url: this.$http.adornUrl("/sys/editDataDictionary"),
          method: "post",
          data: this.$http.adornData({
            fid: this.onePid,
            dataName: this.form.name,
            remarks: this.form.region
          })
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.getDataList();
            this.oneStateRe = "";
            this.form.name = "";
            this.form.region = "";
          } else {
            this.oneStateRe = "";
            this.form.name = "";
            this.form.region = "";
          }
          this.dataListLoading = false;
        });
      } else {
        this.$http({
          url: this.$http.adornUrl("/sys/addDataDictionary"),
          method: "post",
          data: this.$http.adornData({
            dataLevel: 1,
            dataName: this.form.name,
            remarks: this.form.region
          })
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.form.name = "";
            this.form.region = "";
            this.getDataList();
          } else {
          }
          this.dataListLoading = false;
        });
      }
      console.log(111);
    },
    //一级菜单关闭
    cleardialogFormVisible() {
      this.dialogFormVisible = false;
      // this.form.name = "";
      // this.form.region = "";
      console.log(this.form.name);
    },
    //添加一级菜单
    oneAddmenu() {
      this.dialogFormVisible = true;
    },
    //修改一级菜单
    reviseOneList(pid, name, rm) {
      this.dialogFormVisible = true;
      this.oneStateRe = "修改一级";
      this.onePid = pid;
      this.form.name = name;
      this.form.region = rm;
    },
    //获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/queryDataDictionary"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          dataName: this.sizeForm.name
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
    // 获取人员类别
    getPeopleType() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/app/getPersonnelCategory"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.getTrainingCategoryList = data.list;
        } else {
          this.getTrainingCategoryList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    //添加二级分类
    addTwoData(id, name) {
      this.twoDatadialog = true;
      this.formTwo.oneData = name;
      this.formTwo.oneDataId = id;
      this.getPeopleType();
      this.twoDataList();
    },
    // 根据ID查二级菜单数据列表
    twoDataList() {
      this.$http({
        url: this.$http.adornUrl("/sys/getSecClaByID"),
        method: "get",
        params: this.$http.adornParams({
          fid: this.formTwo.oneDataId
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.twotableData = data.list;
        } else {
          this.$message.error("错了哦，请重新填写二级信息");
        }

        this.dataListLoading = false;
      });
    },
    //清空二级分类表单
    getTwoDatadialogClear() {
      this.formTwo.name = "";
      this.formTwo.region = "";
      this.formTwo.oneData = "";
      this.formTwo.TrainingCategory = "";
      this.formTwo.remake = "";
    },
    // 二级菜单提交
    getTwoDatadialogs() {
      this.$http({
        url: this.$http.adornUrl("/sys/addDataDictionary"),
        method: "post",
        data: this.$http.adornData({
          dataName: this.formTwo.name,
          remarks: this.formTwo.remake,
          classification: this.formTwo.oneDataId,
          otherClassification: this.formTwo.TrainingCategory,
          dataLevel: 2,
          sort: this.formTwo.region
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.twoDataList();
          this.$message({
            message: "恭喜你，数据添加成功",
            type: "success"
          });
          this.formTwo.name = "";
          this.formTwo.remake = "";
          this.formTwo.oneDataId = "";
          this.formTwo.TrainingCategory = "";
          this.formTwo.region = "";
        } else {
          this.$message.error("错了哦，请重新填写数据");
          this.getTwoDatadialogClear();
        }
        this.dataListLoading = false;
      });
    },
    // 二级数据修改
    reviseTwoList(e, k, dataName, remarks, sort) {
      console.log(e, k, dataName, remarks);
      this.$router.push({
        name: "twoInfo",
        query: {
          fid: e,
          one: k,
          dataName: dataName,
          remarks: remarks,
          sort: sort
        }
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.TwoList {
  border-top: 1px solid #000;
  margin-top: 5%;
}
</style>
