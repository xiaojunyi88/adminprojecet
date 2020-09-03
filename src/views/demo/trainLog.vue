<template>
  <div>
    <div>
      <el-button @click="getList(null)" type="success">全部人员（{{this.totalPage}}）</el-button>
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
                    <el-button type="primary" size="mini" @click="onSubmit1">查询</el-button>
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
                        style="width:16vw"
                        value-format="yyyy-MM-dd"
                      ></el-date-picker>
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
                    <el-button
                      type="success"
                      size="mini"
                      @click="clearInfo"
                      style="margin-left:10px"
                    >清空</el-button>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="6" :offset="1">
            <div class="grid-content bg-purple">
              <el-row>
                <el-col :span="24">
                  <div class="grid-content bg-purple-dark">
                    <el-form-item label="技能等级">
                      <el-select
                        v-model="form.region"
                        placeholder="请选技能等级"
                        style="width:7vw"
                        @change="getList(form.region)"
                      >
                        <el-option
                          :label="item.dataName"
                          :value="item.fid"
                          v-for="(item,index) in  this.skillsList"
                          :key="item+index"
                        ></el-option>
                      </el-select>
                    </el-form-item>
                  </div>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="1">
            <div class="grid-content bg-purple-light"></div>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <div>
      <el-button
        type="primary"
        @click="addPeople(999)"
        style="float:right;margin-bottom:10px;margin-left:10px"
      >录入人员</el-button>
      <el-button
        type="primary"
        @click="bulkImport"
        style="float:right;margin-bottom:10px;margin-left:10px"
      >批量导入</el-button>
      <el-button
        type="primary"
        @click="certificateTemplate"
        style="float:right;margin-bottom:10px;margin-left:10px"
      >模板下载</el-button>
    </div>
    <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
      <el-table-column prop="id" header-align="center" align="center" label="编号"></el-table-column>
      <el-table-column prop="name" header-align="center" align="center" label="姓名">
        <template slot-scope="{row}">
          <router-link :to="{name:'credentialInfo',query:{id:row.id}}" class="link-type">
            <span>{{ row.name }}</span>
          </router-link>
        </template>
      </el-table-column>
      <el-table-column prop="idCode" header-align="center" align="center" label="身份证号"></el-table-column>
      <el-table-column prop="certificateCode" header-align="center" align="center" label="证书编号"></el-table-column>
      <el-table-column prop="profession" header-align="center" align="center" label="职业资格"></el-table-column>
      <el-table-column prop="skillLevel" header-align="center" align="center" label="技能等级"></el-table-column>
      <el-table-column prop="licenceDate" header-align="center" align="center" label="发证日期"></el-table-column>
      <el-table-column prop="dataUnit" header-align="center" align="center" label="数据责任单位"></el-table-column>
      <el-table-column prop="stutas" header-align="center" align="center" label="状态">
        <template slot-scope="{row}">
          <el-button
            type="text"
            size="small"
            slot="reference"
            @click="addOrUpdateHandle(row.stutas,row.id,row.name)"
          >{{row.stutas===0?"启用":"停用"}}</el-button>
        </template>
      </el-table-column>
      <el-table-column prop="wechatNumber" header-align="center" align="center" label="操作">
        <template slot-scope="{row}">
          <el-button
            type="text"
            size="small"
            slot="reference"
            :disabled="row.stutas===0?false:true"
            @click="addPeople(row.id)"
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
    <el-dialog title="详情" :visible.sync="dialogFormVisible" :before-close="closeRegDialog">
      <h3>基本信息</h3>
      <el-form :model="peopleform" :inline="true" ref="peopleform" :rules="dataRule">
        <el-form-item label="姓名" :label-width="formLabelWidth" prop="name">
          <el-input v-model="peopleform.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="身份证号" :label-width="formLabelWidth" prop="idCard">
          <el-input v-model="peopleform.idCard" autocomplete="off"></el-input>
        </el-form-item>
        <h3>证书信息</h3>
        <el-form-item label="职业资格" :label-width="formLabelWidth" prop="qualifications">
          <el-select v-model="peopleform.qualifications" placeholder="请选择职业资格">
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.qualificationsDataList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="技能等级" :label-width="formLabelWidth" prop="skillItem">
          <el-select v-model="peopleform.skillItem" placeholder="请选技能等级">
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.skillsList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="证书编号" :label-width="formLabelWidth" prop="credentialNumber">
          <el-input v-model="peopleform.credentialNumber" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="发证日期" :label-width="formLabelWidth" prop="value1">
          <el-date-picker
            v-model="peopleform.value1"
            type="date"
            value-format="yyyy-MM-dd"
            placeholder="选择日期"
          ></el-date-picker>
        </el-form-item>
        <el-form-item label="数据责任单位" :label-width="formLabelWidth" prop="accountability">
          <el-select v-model="peopleform.accountability" placeholder="请选择活动区域">
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.accountabilityDataList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="clearPeople">取 消</el-button>
        <el-button type="primary" @click="peoplePost()">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog title="批量导入" :visible.sync="dialogFormVisibleImportant">
      <h3>基本信息</h3>
      <el-form :model="peopleforms" :inline="true">
        <el-form-item label="职业资格" :label-width="formLabelWidth">
          <el-select v-model="peopleforms.qualifications" placeholder="请选择职业资格">
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.qualificationsDataList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="技能等级" :label-width="formLabelWidth">
          <el-select v-model="peopleforms.skillItem" placeholder="请选技能等级">
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.skillsList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="数据责任单位" :label-width="formLabelWidth">
          <el-select v-model="peopleforms.accountability" placeholder="请选择活动区域">
            <el-option
              :label="item.dataName"
              :value="item.fid"
              v-for="(item,index) in  this.accountabilityDataList"
              :key="item+index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="发证日期" :label-width="formLabelWidth">
          <el-date-picker
            v-model="peopleforms.value1"
            type="date"
            value-format="yyyy-MM-dd"
            placeholder="选择日期"
          ></el-date-picker>
        </el-form-item>
        <el-upload
          class="upload-demo"
          :action="url+'?skillLevel='+peopleforms.skillItem+'&profession='+peopleforms.qualifications+'&dataUnit='+peopleforms.accountability+'&licenceDate='+peopleforms.value1"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :before-remove="beforeRemove"
          accept="application/vnd.ms-excel, text/plain"
          multiple
          :limit="3"
          :on-exceed="handleExceed"
          :file-list="fileList"
          :disabled="false"
        >
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">每次最多上传3个文件，并且只能上传excel文件，且不超过5m</div>
        </el-upload>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleImportant = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisibleImportant = false">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dialogFormVisible: false,
      dialogFormVisibleImportant: false,
      qualificationsDataList: [],
      accountabilityDataList: [],
      state: null,
      url: "http://192.168.1.112:8070/vocational-training/certificate/import",
      id: "",
      peopleform: {
        name: "",
        idCard: "",
        qualifications: "", //职业资格
        skillItem: "", //技能等级
        credentialNumber: "", //证书编号
        value1: "",
        accountability: "", //数据责任单位
      },
      peopleforms: {
        name: "",
        idCard: "",
        qualifications: "", //职业资格
        skillItem: "", //技能等级
        credentialNumber: "", //证书编号
        value1: "",
        accountability: "", //数据责任单位
      },
      formLabelWidth: "140px",
      form: {
        name: "",
        region: "",
        value1: "",
        TrainingCategory: "",
      },
      skillsList: [],
      dataList: [],
      getPersonnelCategoryList: [], // 人员类别
      getTrainingCategoryList: [], //职业技能培训
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      fileList: [],
      dataListLoading: false,
      dataRule: {
        name: [{ required: true, message: "姓名不能为空", trigger: "blur" }],
        idCard: [
          { required: true, message: "身份证号不能为空", trigger: "blur" },
        ],
        qualifications: [
          { required: true, message: "职业资格不能为空", trigger: "blur" },
        ],
        skillItem: [
          { required: true, message: "技能等级不能为空", trigger: "blur" },
        ],
        credentialNumber: [
          { required: true, message: "证书编号不能为空", trigger: "blur" },
        ],
        value1: [{ required: true, message: "日期不能为空", trigger: "blur" }],
        accountability: [
          { required: true, message: "数据责任单位不能为空", trigger: "blur" },
        ],
      },
    };
  },
  created() {
    this.getList();
    this.getPersonnelCategory();
    this.qualifications(); //职业资格获取数据
    this.skills(); //技能等级
    this.getaccountability(); //责任单位数据
  },
  methods: {
    //清空人员数据
    clearPeople() {
      (this.dialogFormVisible = false),
        (this.peopleform.name = ""),
        (this.peopleform.credentialNumber = ""),
        (this.peopleform.idCard = ""),
        (this.peopleform.qualifications = ""),
        (this.peopleform.skillItem = ""),
        (this.peopleform.value1 = ""),
        (this.peopleform.accountability = "");
    },
    //录入人员提交
    peoplePost() {
      this.$refs["peopleform"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl("/certificate/save"),
            method: "post",
            data: this.$http.adornData({
              id: this.id,
              name: this.peopleform.name,
              certificateCode: this.peopleform.credentialNumber,
              idCode: this.peopleform.idCard,
              profession: this.peopleform.qualifications,
              skillLevel: this.peopleform.skillItem,
              licenceDate: this.peopleform.value1,
              dataUnit: this.peopleform.accountability,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$nextTick(() => {
                    this.getList();
                    this.clearPeople();
                  });
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
          this.dialogFormVisible = false;
        } else {
          this.dialogFormVisible = true;
          console.log(20202);
        }
      });
    },
    StudentStatus(row, column) {
      if (row.stutas === 1) {
        return "停用";
      } else {
        return "启用";
      }
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePreview(file) {
      console.log(file);
    },
    // 数据责任单位
    getaccountability() {
      console.log(111);

      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/getSecClaByID"),
        method: "get",
        params: this.$http.adornParams({
          fid: 41,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.accountabilityDataList = data.list;
        } else {
          this.accountabilityDataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    //技能等级
    skills() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/getSecClaByID"),
        method: "get",
        params: this.$http.adornParams({
          fid: 40,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.skillsList = data.list;
        } else {
          this.skillsList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    //职业资格
    qualifications() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/sys/getSecClaByID"),
        method: "get",
        params: this.$http.adornParams({
          fid: 39,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.qualificationsDataList = data.list;
        } else {
          this.qualificationsDataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    handleExceed(files, fileList) {
      this.$message.warning(
        `当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${
          files.length + fileList.length
        } 个文件`
      );
    },
    beforeRemove(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    },

    getList(e) {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/certificate/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.form.name,
          beginTime: "",
          endTime: "",
          skillLevel: e,
          page: this.pageIndex,
          limit: this.pageSize,
        }),
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
    //导出表格
    ecxl() {
      let params = {};
      this.$http({
        url: this.$http.adornUrl("/sys/trainerExport"),
        method: "get",
        responseType: "blob",
        params: this.$http.adornParams({}),
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
    closeRegDialog() {
      this.dialogFormVisible = false;
      this.$refs["peopleform"].resetFields();
    },
    certificateTemplate() {
      let params = {};
      this.$http({
        url: this.$http.adornUrl("/certificate/export"),
        method: "get",
        responseType: "blob",
        params: this.$http.adornParams({}),
      })
        .then((res) => {
          const blob = new Blob([res.data], {
            type: "application/vnd.ms-excel",
          });
          const fileName = "批量导入模板.xls";
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
    //添加人员
    addPeople(id) {
      console.log(id === 999, 999);
      if (id === 999) {
        this.id = "";
      } else {
        this.id = id;
      }
      this.dialogFormVisible = true;
      this.$http({
        url: this.$http.adornUrl("/certificate/info"),
        method: "get",
        params: this.$http.adornParams({
          id: this.id,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.peopleform.name = data.certificateVO.name;
          this.peopleform.idCard = data.certificateVO.idCode;
          this.peopleform.qualifications = data.profession.dataName;
          this.peopleform.skillItem = data.skill.dataName;
          this.peopleform.value1 = data.certificateVO.licenceDate;
          this.peopleform.credentialNumber = data.certificateVO.certificateCode;
          this.peopleform.accountability = data.dataUnit.dataName;
        } else {
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    //批量导入
    bulkImport() {
      this.dialogFormVisibleImportant = true;
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
    // 时间查询
    getTime() {
      this.form.name = "";
      console.log(this.form.value1.slice(0));

      this.$http({
        url: this.$http.adornUrl("/certificate/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.form.name,
          beginTime: this.form.value1[0],
          endTime: this.form.value1[1],
          skillLevel: "",
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
    onSubmit1() {
      console.log(222);
      this.pageIndex = 1;
      this.pageSize = 10;
      this.$http({
        url: this.$http.adornUrl("/certificate/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.form.name,
          beginTime: this.form.value1[0],
          endTime: this.form.value1[1],
          skillLevel: "",
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
    onSubmit() {
      console.log(this.form.value1);
      this.$http({
        url: this.$http.adornUrl("/certificate/list"),
        method: "get",
        params: this.$http.adornParams({
          name: this.form.name,
          beginTime: this.form.value1[0],
          endTime: this.form.value1[1],
          skillLevel: "",
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
    addOrUpdateHandle(e, id, userIds) {
      console.log(e);
      this.state = e;
      this.state === 0 ? 1 : 0;
      console.log(this.state, 111);

      this.$confirm(`确定对${userIds}进行${e ? "启用" : "停用"}操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/certificate/switch"),
            method: "post",
            data: this.$http.adornData({
              id: id,
              stutas: this.state === 0 ? 1 : 0,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.getList();
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        })
        .catch(() => {});
    },
    // 获取人员类别
    getPersonnelCategory() {
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
  },
};
</script>

<style lang="scss" scoped>
.el-row {
  margin-top: 20px;
  width: 100%;
  &:last-child {
    margin-bottom: 0;
  }
}
.contain {
  object-fit: contain;
  width: 50px;
}
</style>
