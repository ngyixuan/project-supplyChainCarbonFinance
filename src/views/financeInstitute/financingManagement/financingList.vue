<template>
  <div class="sub-content-box">
    <header-title :headerTitle="headerTitle"></header-title>
    <div></div>
    <div class="sub-content-tabs">
      <el-tabs v-model="activeName">
        <el-tab-pane label="保理待审批" name="first">
          <template>
            <div>
              <list-table :data="pendingData" :columns="column">
                <!-- 插槽1：状态 -->
                <template #status="{ row, $index }">
                  <el-tag v-if="row.approved" class="approved">已审核</el-tag>
                  <el-tag v-else class="not-approved">待审核</el-tag>
                </template>
                <template #option="{ row, $index }">
                  <el-checkbox @change="getrows(row)" name="type"></el-checkbox>
                </template>
              </list-table>
            </div>
          </template>
          <div class="sub-content-import-export">
            <button @click="sendRow()" class="button-style">查看</button>
          </div>
        </el-tab-pane>
        <el-tab-pane label="保理已审批">
          <template>
            <div>
              <list-table :data="signingData" :columns="column">
                <!-- 插槽1：状态 -->
                <template #status="{ row, $index }">
                  <el-tag class="approved">已审核</el-tag>
                </template>
                <template #option="{ row, $index }">
                  <el-checkbox @change="getrows(row)" name="type"></el-checkbox>
                </template>
              </list-table>
            </div>
          </template>
          <div class="sub-content-import-export">
            <button @click="sendRow()" class="button-style">查看</button>
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
  loadInstitutePendingFactors,
  loadInstituteApprovedFactors,
} from "@/utils/api.js";
export default {
  data() {
    return {
      institution: localStorage.getItem("name"),
      ID: [],
      row: {},
      headerTitle: {
        largeTitle: "融资管理",
        smallTitle: "融资列表",
      },

      activeName: "first",
      column: [
        {
          prop: "option",
          label: "选择",
          width: "100",
          customSlot: "option",
        },

        {
          prop: "company",
          label: "融资企业",
          width: "",
        },
        {
          prop: "amount",
          label: "融资金额",
          width: "",
        },
        {
          prop: "time",
          label: "操作时间",
          width: "",
        },
        {
          prop: "status",
          label: "碳信账户状态",
          customSlot: "status",
        },
      ],
      pendingData: [],
      signingData: [],
    };
  },
  async mounted() {
    console.log(this.institution);
    const { data: submitted } = await loadInstitutePendingFactors(
      this.institution
    );
    this.pendingData = submitted.data;
    const { data: pending } = await loadInstituteApprovedFactors(
      this.institution
    );
    this.signingData = pending.data;
    console.log(this.pendingData);
  },
  methods: {
    getrows(row) {
      this.row = row;
      // //console.log(row.name);
    },

    // 发送ID
    sendRow() {
      console.log(this.row.status);
      if (this.row.status == 1) {
        this.$router.push({
          path: `/financeInstitute/financingManagement/factoringBuying/${this.row.id}`,
        });
      } else {
        this.$router.push({
          path: `/financeInstitute/financingManagement/factoringApproval/${this.row.id}`,
        });
      }
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
