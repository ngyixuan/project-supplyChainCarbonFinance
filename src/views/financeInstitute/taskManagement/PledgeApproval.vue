<template>
  <div class="sub-content-box">
    <header-title :headerTitle="headerTitle"></header-title>

    <div class="sub-content-body">
      <div class="description-title">
        <div class="table-rec"></div>
        审核信息
      </div>

      <div class="description-box">
        <el-descriptions>
          <el-descriptions-item label="配额所属供应链">
            {{ this.pledgeDetail.controlChain }}
          </el-descriptions-item>
          <el-descriptions-item label="配额量">
            {{ this.pledgeDetail.quotaQuantity }}
          </el-descriptions-item>
          <el-descriptions-item label="附件"> 合同.pdf </el-descriptions-item>
          <el-descriptions-item label="配额所有者">
            {{ this.pledgeDetail.quotaOwner }}
          </el-descriptions-item>
          <el-descriptions-item label="资金用途">
            {{ this.pledgeDetail.fundUse }}
          </el-descriptions-item>
        </el-descriptions>
      </div>

      <div class="description-title">
        <div class="table-rec"></div>
        审批操作
      </div>
      <div class="radio-approval-box" @click="getId">
        <el-radio v-model="radio" label="1" @change="updateComment(1)"
          >审核</el-radio
        >
        <el-radio v-model="radio" label="2" @change="updateComment(2)"
          >拒绝</el-radio
        >
        <div class="radio-approval-comment-title">签署意见</div>
        <div class="radio-approval-comment-content">
          <el-input
            type="textarea"
            :rows="8"
            placeholder="请输入内容"
            v-model="textarea"
          >
          </el-input>
        </div>

        <button class="button-style" @click="dialogVisible = true">提交</button>
        <!-- 提交弹出操作密码面板 -->
        <el-dialog title="操作密码" :visible.sync="dialogVisible" width="30%">
          <el-form
            :model="action"
            status-icon
            :rules="rules"
            ref="action"
            label-width="100px"
            class="demo-action"
          >
            <el-form-item label="密码" prop="actionPassword">
              <el-input
                type="password"
                v-model="action.actionPassword"
                autocomplete="off"
              ></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="submitForm(action)"
                >提交</el-button
              >
            </el-form-item>
          </el-form>
        </el-dialog>
        <!-- 操作密码面板结束 -->
      </div>
    </div>
  </div>
</template>
<script>
import headerTitle from "@/components/headerTitle.vue";
import editableText from "@/components/editableText.vue";
import {
  updateInstitutionPledgeExamination,
  loadSinglePledgeRow,
} from "@/utils/api.js";
export default {
  data() {
    var validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
      } else {
        if (this.action.checkPass !== "") {
          this.$refs.action.validateField("checkPass");
        }
        callback();
      }
    };
    return {
      institution: localStorage.getItem("name"),
      id: "",
      dialogVisible: false,
      pledgeDetail: "",
      headerTitle: {
        largeTitle: "任务管理",
        smallTitle: "质押审批",
      },
      edit: false,
      text: [
        {
          id: 1,
          label: "配额所属供应链",
          input: this.institution,
        },
        {
          id: 2,
          label: "配额量",
          input: "XXXXX",
        },
        {
          id: 3,
          label: "附件",
          input: "合同.pdf",
        },
        {
          id: 4,
          label: "配额所有者",
          input: "配额所有者",
        },
        {
          id: 5,
          label: "资金用途",
          input: "无",
        },
      ],
      action: {
        accountName: localStorage.getItem("name"),
        accountType: localStorage.getItem("accountType"),
        actionPassword: "",
        id: "",
        comment: "",
      },
      radio: "1",
      textarea: "",
      rules: {
        actionPassword: [{ validator: validatePass, trigger: "blur" }],
      },
    };
  },
  async mounted() {
    this.pledgeID = parseInt(this.$route.params.id);
    const { data: res } = await this.$http.get(
      "/pledgeSearch/" + this.pledgeID
    );
    this.pledgeDetail = res.data;
    this.action.id = this.pledgeID;
    console.log(this.pledgeDetail);
    console.log(res);
    if (this.radio == "1") {
      this.action.comment = true;
    } else {
      this.action.comment = false;
    }
    console.log(this.radio);
  },

  methods: {
    updateComment(label) {
      this.action.comment = label == 1;
      console.log(this.action);
    },

    getId() {
      let currID = this.$route.params.id;
      this.id = currID;
    },

    submitForm(action) {
      this.$refs.action.validate(async (valid) => {
        if (valid) {
          this.dialogVisible = false;
          console.log(this.action);

          updateInstitutionPledgeExamination(action).then((data) => {
            console.log(data);
            if (data.data.conde != 0) {
              this.dialogVisible = false;
              this.$message({
                message: "密码不正确",
                type: "warning",
              });
            } else {
              this.$message({
                message: "完成审批",
                type: "success",
              });
            }
          });
        }
      });
    },
  },
  components: {
    headerTitle,
    editableText,
  },
};
</script>
<style scoped>
.sub-content-body {
  background: #fff;
  margin: 20px;

  border-radius: 25px;
}

.description-box {
  border: 1px solid #eee;
  margin: 10px 40px;
  border-radius: 12px;
}

.description-title {
  font-size: 18px;
  padding-top: 20px;
  margin-left: 40px;
  margin-bottom: 30px;
  line-height: 30px;
}
.description-title .table-rec {
  float: left;
  width: 4px;
  height: 30px;
  border-radius: 50px;
  background-color: #f2a626;
  margin-right: 10px;
}
::v-deep .el-descriptions {
  padding: 20px 20px;
}
::v-deep .el-radio__input.is-checked .el-radio__inner {
  border-color: #156a59;
  background: #156a59;
}
::v-deep .el-radio__input.is-checked + .el-radio__label {
  color: #156a59;
}
.radio-approval-box {
  margin-left: 40px;
  margin-top: 30px;
}
.radio-approval-comment-title {
  font-size: 14px;
  margin: 20px 0px 15px 0px;
  color: #6e7191;
}
.radio-approval-comment-content {
  height: 150px;
  width: calc(100vw - 380px);
  border: 1px solid #eee;
  margin: 15px 0px 30px 0px;
}

.approval-button {
  margin: 20px 40px;
}
.button-style {
  margin: 30px 20px 50px 0px;
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
</style>
