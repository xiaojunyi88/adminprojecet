<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" class="separator">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/serviceProject' }">服务项目管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加服务项目</el-breadcrumb-item>
    </el-breadcrumb>
    <hr />
    <el-form
      :model="dataForm"
      :rules="rules"
      ref="ruleForm"
      label-width="130px"
      class="demo-ruleForm"
      @keyup.enter.native="getDataList()"
    >
      <div class="basic-info">
        <div class="basic-title">基本信息</div>
        <el-form-item label="服务项目名称" prop="name">
          <el-input v-model="dataForm.name" style="width:300px"></el-input>
        </el-form-item>
        <el-form-item label="窗口数量" prop="count">
          <el-select v-model="dataForm.count" placeholder="请选择活动区域" style="width:300px">
            <el-option label="1" value="1"></el-option>
            <el-option label="2" value="2"></el-option>
          </el-select>
        </el-form-item>
      </div>
      <div class="service-info">
        <div class="basic-title">服务详情</div>
        <el-form-item label="排序" prop="sort">
          <el-select v-model="dataForm.sort" placeholder="请选择排序" style="width:300px">
            <el-option
              :label="item"
              :value="item"
              v-for="(item,index) in dataForm.serviceItemSortList "
              :key="index"
            ></el-option>
          </el-select>
        </el-form-item>
        <!-- 周期模式 -->
        <div class="week">
          <div class="w-top">
            <el-form-item label="周期模式" prop="radio" style="margin-bottom:0;">
              <el-radio disabled v-model="dataForm.radio" label="1">固定时段</el-radio>
              <el-radio disabled v-model="dataForm.radio" label="2">按每周</el-radio>
              <el-radio disabled v-model="dataForm.radio" label="3">按每天</el-radio>
            </el-form-item>
          </div>
          <div class="content01">
            <p>每天同一时段使用相同的设置。-1表示不限数量。</p>
            <el-row>
              <el-col :span="9">
                <h4>时段</h4>
              </el-col>
              <el-col :span="8">
                <h4>最大预约数</h4>
              </el-col>
              <el-col :span="7">
                <h4 style="text-align:left">操作</h4>
              </el-col>
            </el-row>
            <div class="content-text">
              <el-row>
                <el-col :span="9">
                  <el-form-item
                    label="时段"
                    style="margin-bottom:0;"
                    :rules="{
                 required: true, message: '时段不能为空', trigger: 'blur'
                 }"
                  >
                    <el-time-select
                      placeholder="起始时间"
                      v-model="dataForm.List.startTimesss"
                      :picker-options="{
                        start: '08:30',
                        step: '00:15',
                        end: '18:30'
                      }"
                    ></el-time-select>
                    <el-time-select
                      placeholder="结束时间"
                      v-model="dataForm.List.endTime"
                      :picker-options="{
                        start: '08:30',
                        step: '00:15',
                        end: '18:30',
                        minTime: startTime
                      }"
                    ></el-time-select>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item
                    label="最大预约数"
                    :rules="{
                     required: true, message: '最大预约数不能为空', trigger: 'blur'
                 }"
                  >
                    <el-select v-model="dataForm.List.num" placeholder="请选择预约人数" style="width:3rem">
                      <el-option label="1" value="1"></el-option>
                      <el-option label="2" value="2"></el-option>
                      <el-option label="3" value="3"></el-option>
                      <el-option label="4" value="4"></el-option>
                      <el-option label="5" value="5"></el-option>
                      <el-option label="6" value="6"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="7">
                  <el-button type="success" @click="addList">添加时段</el-button>
                </el-col>
              </el-row>
            </div>
            <div class="time-list">
              <!-- 循环列表 -->
              <el-row
                v-for="(item, index) in dataForm.timeList"
                :key="index"
                style="border-bottom:1px solid #eee"
              >
                <el-col :span="8" style="text-align:center;line-height:0.6rem">
                  <el-time-select
                    placeholder="起始时间"
                    v-model="item.startPeriod"
                    value-format="HH:mm"
                    :picker-options="{
                                        start: '08:30',
                                        step: '00:15',
                                        end: '18:30'
                                      }"
                    @change="edited(index,item.startPeriod)"
                  ></el-time-select>
                  <el-time-select
                    placeholder="结束时间"
                    v-model="item.endPeriod"
                    value-format="HH:mm"
                    :picker-options="{
                                        start: '08:30',
                                        step: '00:15',
                                        end: '18:30',
                                        minTime: startTime
                                      }"
                  ></el-time-select>
                </el-col>
                <el-col :span="8">
                  <!-- <h4>{{item.maxPersonNum}}</h4> -->
                  <el-form-item
                    label="最大预约数"
                    :rules="{
                     required: true, message: '最大预约数不能为空', trigger: 'blur'
                 }"
                    style="margin-top:0.1rem"
                  >
                    <el-select v-model="item.maxPersonNum" placeholder="请选择预约人数" style="width:3rem">
                      <el-option label="1" value="1"></el-option>
                      <el-option label="2" value="2"></el-option>
                      <el-option label="3" value="3"></el-option>
                      <el-option label="4" value="4"></el-option>
                      <el-option label="5" value="5"></el-option>
                      <el-option label="6" value="6"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-link @click="deleteTodo(index)" style="margin:0 auto;line-height:0.6rem">删除</el-link>
                </el-col>
              </el-row>
            </div>
          </div>
        </div>
        <!-- 预定范围 -->
        <el-row style="margin-top:1rem">
          <el-col :span="6">
            <el-form-item label="预定范围">
              <el-select
                v-model="dataForm.appointmentScopeSui"
                placeholder="随时"
                style="width:300px"
                disabled
              >
                <el-option label="随时" value="1"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="18">
            <el-form-item label="预约到">
              <el-input v-model="dataForm.appointmentScope" style="width:1rem"></el-input>
              <span>天内的服务</span>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="6">
            <el-form-item label="取消预约">
              <el-select
                v-model="dataForm.cancelReservation"
                placeholder="是"
                disabled
                style="width:300px"
              >
                <el-option label="1" value="1"></el-option>
                <el-option label="2" value="2"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="18">
            <el-form-item label="可提前">
              <el-input v-model="dataForm.cancellationHoursAdvance" style="width:1rem"></el-input>
              <el-select v-model="dataForm.canceHours" style="width:1rem" disabled>
                <el-option label="小时" value="1"></el-option>
                <el-option label="天" value="2"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </div>
      <div>
        <el-button type="info" @click="fanhui">返回</el-button>
        <el-button type="success" @click="update">保存</el-button>
      </div>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pid: "",
      startTime: "",
      dataForm: {
        name: "",
        count: "",
        sort: "",
        radio: "1",
        cancelReservation: "",
        appointmentScope: "",
        cancellationHoursAdvance: "",
        serviceItemSortList: [],
        reviseInfo: [],
        List: {
          maxPersonNum: "",
          pid: "11",
          masterid: "1",
          endandstartTime: [
            new Date(2016, 9, 10, 8, 40),
            new Date(2016, 9, 10, 9, 40)
          ]
        },
        timeList: [new Date(2016, 9, 10, 8, 40), new Date(2016, 9, 10, 9, 40)]
      },

      rules: {
        name: [
          { required: true, message: "请输入服务项目名称", trigger: "blur" },
          { min: 1, max: 12, message: "长度在 1 到 12 个字符", trigger: "blur" }
        ],
        count: [{ required: true, message: "请输入窗口数", trigger: "change" }],
        sort: [{ required: true, message: "请选择排序", trigger: "change" }],
        sorts: [{ required: true, message: "请输入天数", trigger: "change" }],
        sortss: [{ required: true, message: "请输入天数", trigger: "change" }],
        radio: [
          { required: true, message: "请选择活动区域", trigger: "change" }
        ],
        startTime: [
          { required: true, message: "请选择开始时间", trigger: "change" }
        ]
        // rulesList: {
        //   startTime: [
        //     { required: true, message: "请选择活动区域", trigger: "change" }
        //   ]
        // }
      }
    };
  },
  created() {
    this.reviseInfo();
    this.SortList();
  },
  mounted() {
    console.log(this.pid);
  },
  methods: {
    test(i) {
      console.log(i);

      // this.dataForm.List[i].startTimesss = this.dataForm.List.startTimesss;
      console.log(this.dataForm.List);
    },
    addList() {
      let todoObj = {
        startPeriod: this.dataForm.List.startTimesss,
        endPeriod: this.dataForm.List.endTime,
        maxPersonNum: this.dataForm.List.num
      };
      this.dataForm.timeList.push(todoObj);
      (this.dataForm.List.startTimesss = ""),
        (this.dataForm.List.endTime = ""),
        (this.dataForm.List.num = "");
      console.log(this.dataForm.timeList, "11111111111111111111");
    },
    deleteTodo(index) {
      this.dataForm.timeList.splice(index, 1);
    },
    edited(i, todo) {
      // this.cursor_id = null;
      // this.todos[parseInt(i)] = todo;
      this.dataForm.timeList[i].startPeriod = todo;
    },
    // 上页修改
    reviseInfo() {
      this.pid = this.$route.query.pid;
      this.$http({
        url: this.$http.adornUrl(
          "/ServiceProjectManagement/queryByIDServiceItem"
        ),
        method: "get",
        params: this.$http.adornParams({
          pid: this.pid
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.reviseInfo = data.data[0];
          if (this.$route.query.pid) {
            this.dataForm.name = this.reviseInfo.serviceItemName || "";
            this.dataForm.count = this.reviseInfo.numberOfWindows;
            this.dataForm.sort = this.reviseInfo.serviceItemSort;
            this.dataForm.timeList = this.reviseInfo.fromTableData;
            this.dataForm.appointmentScope = this.reviseInfo.appointmentScope;
            this.dataForm.cancelReservation = this.reviseInfo.cancelReservation;
            this.dataForm.canceHours = "小时";
            this.dataForm.cancellationHoursAdvance = this.reviseInfo.cancellationHoursAdvance;
            console.log(this.reviseInfo, 1);
          }
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 排序接口
    SortList() {
      this.$http({
        url: this.$http.adornUrl(
          "/ServiceProjectManagement/serviceItemSortList"
        ),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataForm.serviceItemSortList = Object.keys(
            data.serviceItemSortList
          );
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },

    update() {
      var that = this;
      that
        .$http({
          url: that.$http.adornUrl("/ServiceProjectManagement/update"),
          method: "post",
          data: that.$http.adornData({
            pid: this.pid,
            serviceItemName: that.dataForm.name,
            dailyReception: 15,
            businessStatus: "启用",
            serviceItemSort: that.dataForm.sort,
            numberOfWindows: that.dataForm.count,
            appointmentScope: that.dataForm.appointmentScope,
            cancelReservation: that.dataForm.cancelReservation,
            periodicMode: "正常",
            cancellationHoursAdvance: that.dataForm.cancellationHoursAdvance,
            fromTableData: that.dataForm.timeList
          })
        })
        .then(({ data }) => {
          if (data.msg == "success") {
            that.$message({
              message: "恭喜你，保存成功",
              type: "success"
            });

            setTimeout(() => {
              console.log(that);
              console.log("要延迟的代码");
              that.$router.back(-1);
            }, 1000);
          }
        });
    },
    fanhui() {
      this.$router.back(-1);
    }
  }
};
</script>

<style lang="scss" scoped>
.basic-title {
  border-bottom: 1px solid #f2f2f2;
  margin-bottom: 2vh;
  padding-bottom: 0.18rem;
  margin-top: 0.5rem;
  font-weight: 600;
}
.week {
  .content01 {
    overflow: hidden;
    p {
      margin: 0;
      padding: 0;
      font-size: 12px;
      color: #929292;
      box-sizing: border-box;
      padding-left: 1.3rem;
      padding-bottom: 0.1rem;
    }
    .time-list {
      box-sizing: border-box;
      margin-left: 1rem;
      h4 {
        text-align: center;
      }
    }
  }
}
</style>
