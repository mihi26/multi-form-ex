<template>
  <div class="form-wrapper">
    <CompanyForm
      v-for="company in expForm"
      :company="company"
      :listCompany="listCompany"
      @removeCompany="removeCompany"
    />
    change
  </div>
  <div v-if="companyError.status" class="error-label">
    {{ companyError.text }}
  </div>
  <button type="button" class="button button-add-company" @click="addCompany">
    <img alt="plus" src="../assets/plus.svg" />
    Thêm công ty
  </button>
  <div class="button-wrapper">
    <button type="button" class="button button-next" @click="nextStep">
      Tiếp
    </button>
    <button type="button" class="button button-back" @click="backStep">
      Quay lại
    </button>
  </div>
</template>
<script>
import CompanyForm from "./CompanyForm.vue";
import isBefore from "date-fns/isBefore";
import { isEqual, parseISO } from "date-fns";
export default {
  name: "UserExperienceForm",
  components: {
    CompanyForm,
  },
  emits: ["nextStep", "backStep", "finishForm"],
  data() {
    return {
      companyError: {
        status: false,
        text: "",
      },
      listCompany: [
        {
          id: 1,
          name: "Mor Software",
        },
        {
          id: 2,
          name: "Hehh",
        },
        {
          id: 3,
          name: "Whatt",
        },
      ],
      countId: 0,
      expForm: [
        {
          id: 0,
          userCompany: "Mor Software",
          userPosition: "",
          dateStart: null,
          dateEnd: null,
          jobDesc: "",
        },
      ],
    };
  },
  methods: {
    nextStep() {
      if (this.companyError.status) this.companyError.status = false;
      let valid = true;
      for (let company of this.expForm) {
        if (
          !company.userPosition.length ||
          !company.dateStart ||
          !company.dateEnd
        ) {
          this.companyError = {
            text: "Không được để trống những mục bắt buộc",
            status: true,
          };
          valid = false;
          break;
        } else if (
          isEqual(parseISO(company.dateStart), parseISO(company.dateEnd))
        ) {
          this.companyError.status.false;
        } else if (
          !isBefore(parseISO(company.dateStart), parseISO(company.dateEnd))
        ) {
          this.companyError = {
            status: true,
            text: "Ngày bắt đầu phải ở trước ngày kết thúc",
          };
          valid = false;
          break;
        }
      }
      if (valid) {
        this.$emit("finishForm", this.expForm);
        this.$emit("nextStep");
      }
    },
    backStep() {
      if (this.companyError.status) this.companyError.status = false;
      this.$emit("backStep");
    },
    addCompany() {
      if (this.companyError.status) this.companyError.status = false;
      this.expForm.push({
        id: ++this.countId,
        userCompany: "Mor Software",
        userPosition: "",
        dateStart: null,
        dateEnd: null,
      });
    },
    removeCompany(id) {
      if (this.expForm.length === 1) {
        this.companyError = {
          status: true,
          text: "Phải có tối thiểu 1 công ty",
        };
      } else this.expForm = this.expForm.filter((company) => company.id !== id);
    },
  },
};
</script>
<style lang="scss" scoped>
.form-wrapper {
  display: flex;
  flex-direction: column;
  gap: 28px;
}
.button {
  width: 102px;
  height: 40px;
  font-size: 16px;
  line-height: 24px;
  text-align: center;
  border-radius: 3px;
  border: none;
  cursor: pointer;
  &-next {
    background-color: #627d98;
    font-weight: 700;
    color: #ffffff;
  }
  &-back {
    background-color: #ffffff;
    border: 1px solid #dcdcdc;
    font-weight: 500;
    color: #666666;
  }
  &-add-company {
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: 8px;
    width: 151px;
    height: 40px;
    background: #ffffff;
    border: 1px solid #dcdcdc;
    color: #48647f;
    font-family: "Noto Sans";
    margin-top: 20px;
  }
}
.button-wrapper {
  display: flex;
  gap: 26px;
  margin-top: 24px;
}
.error-label {
  color: #ed5d5d;
  font-size: 14px;
  line-height: 20px;
  margin-top: 10px;
}
</style>
