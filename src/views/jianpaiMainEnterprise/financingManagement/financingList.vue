<template>
  <div class="sub-content-box">
    <header-title :headerTitle="headerTitle"></header-title>

    <div class="sub-content-tabs">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <!-- <el-tab-pane label="全部" name="first">
          
        </el-tab-pane> -->
        <el-tab-pane label="申请中" name="first">
          <template>
            <div>
              <list-table :data="pendingData" :columns="column">
                <!-- 插槽1：状态 -->
                <template #status="{ row, $index }">
                  <!-- <el-tag v-if="row.approved" class="approved">可签约</el-tag> -->
                  <el-tag v-if="row.status == 0" class="not-approved"
                    >申请中</el-tag
                  >
                  <el-tag v-else class="approved">哈哈</el-tag>
                </template>

                <template #option="{ row, $index }">
                  <el-checkbox name="type" @change="getrows(row)"></el-checkbox>
                </template>
              </list-table>
            </div>
          </template>
          <!-- <div class="sub-content-import-export">
            <button class="button-style" @click="sendRow()">查看</button>
          </div> -->
        </el-tab-pane>
        <el-tab-pane label="可签约" name="second">
          <template>
            <div>
              <list-table :data="waitingSigningData" :columns="column">
                <!-- 插槽1：状态 -->
                <template #status="{ row, $index }">
                  <!-- <el-tag v-if="row.approved" class="approved">可签约</el-tag> -->
                  <el-tag class="approved">可签约</el-tag>
                </template>

                <template #option="{ row, $index }">
                  <el-checkbox name="type" @change="getrows(row)"></el-checkbox>
                </template>
              </list-table>
            </div>
          </template>
          <div class="sub-content-import-export">
            <button class="button-style" @click="sendRow()">查看</button>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>
<script>
import headerTitle from "@/components/headerTitle.vue";
import ListTable from "@/components/ListTable";
import {
  loadCompanyApplyingFactors,
  loadCompanySigningFactors,
} from "@/utils/api.js";

export default {
  data() {
    return {
      selectedID: "",
      company: localStorage.getItem("name"),
      chain: localStorage.getItem("chain"),
      headerTitle: {
        largeTitle: "融资管理",
        smallTitle: "融资列表",
      },
      row: {},
      activeName: "first",
      column: [
        {
          prop: "option",
          label: "选择",
          width: "100",
          customSlot: "option",
        },
        {
          prop: "institution",
          label: "融资对象",
          width: "",
        },
        {
          prop: "time",
          label: "申请时间",
          width: "",
        },
        {
          prop: "amount",
          label: "碳信数额",
          width: "",
        },
        {
          prop: "status",
          label: "签收状态",
          customSlot: "status",
        },
      ],

      pendingData: [],
      waitingSigningData: [],
    };
  },

  async mounted() {
    // `status` int NULL DEFAULT NULL COMMENT '1:申请中，2:通过审核，3:正在签约，4:完成签约，5:失败'
    const { data: pending } = await loadCompanyApplyingFactors(this.company);
    this.pendingData = pending.data;
    console.log(pending.data);

    const { data: signing } = await loadCompanySigningFactors(this.company);
    this.waitingSigningData = signing.data;
    console.log(signing.data);
  },
  methods: {
    //获取单行数据
    getrows(row) {
      this.row = row;
      console.log(this.row.status);
      // this.selectedID = row.id;
      // console.log(this.selectedID);
    },
    // 发送ID
    sendRow() {
      console.log(this.row.id);
      this.$router.push({
        path: `/jianpaiMainEnterprise/financingManagement/financingSigning/${this.row.id}`,
      });
      //console.log(this.row.task);
    },
    handleClick(tab, event) {
      //console.log(tab, event);
    },
  },
  components: {
    headerTitle,
    ListTable,
  },
};
</script>
<style scoped>
.sub-content-tabs {
  margin: 20px 40px 40px;
}

.sub-content-import-export {
  padding-top: 20px;
}

.sub-content-import-export .el-button {
  width: 100px;
  height: 35px;
  border-radius: 7px;
  padding: 0;
  font-size: 14px;
}

.button-style {
  margin-right: 20px;
  width: 100px;
  height: 33px;
  border-radius: 7px;
  transition: 0.1s;
  border: none;
  text-align: center;
  box-sizing: border-box;
  background: #209f85;
  color: #fff;
}

.button-style:hover {
  background: transparent;
  border: 1px solid #209f85;
  color: #209f85;
  cursor: pointer;
}

::v-deep .el-input__inner {
  border-radius: 20px;
  height: 34px;
  padding-left: 20px;
}

::v-deep .el-tabs__item.is-active {
  color: #209f85;
}
::v-deep .el-tabs__item :hover {
  color: #209f85;
}

::v-deep .el-tabs__active-bar {
  background-color: #209f85;
}
::v-deep .el-tabs__nav-wrap::after {
  background-color: #f5f7fa;
}
.approved {
  background-color: #dfefff;
  color: #369afe;
  font-weight: bold;
  width: 100px;
}
.not-approved {
  background-color: #ffeaea;
  color: #ff4b4b;
  font-weight: bold !important;
  width: 100px;
  border-color: #ffeaea;
}
::v-deep .el-checkbox__input.is-checked .el-checkbox__inner {
  background-color: #209f85 !important;
  border-color: #209f85 !important;
}
::v-deep .el-checkbox__input.is-indeterminate .el-checkbox__inner {
  background-color: #209f85;
  border-color: #209f85;
}
</style>
