<template>
  <div class="wish-form">
    <div class="wish-form-info">
      <label for="company-reason" class="company-reason"
        >Lý do ứng tuyển vào công ty</label
      >
      <textarea
        name="company-reason"
        class="company-reason-textarea"
        :class="{ 'error-input': error.reason.status }"
        v-model="wishForm.companyReason"
        @input="checkReason"
        @blur="checkReason"
      />
      <div
        class="company-reason-count"
        :class="{ 'error-count': error.reason.status }"
      >
        {{ charCount }}/1000
      </div>
      <div v-if="error.reason.status" class="error-label">
        {{ error.reason.text }}
      </div>
      <label for="company-salary" class="company-salary">
        Mức lương mong muốn
      </label>
      <div
        class="salary-wrapper"
        :class="{ 'error-input': error.salary.status }"
      >
        <input
          id="company-salary"
          name="company-salary"
          class="company-salary-input"
          v-model="wishForm.companySalary"
          @input="checkSalary"
          @blur="checkSalary"
        />
        <span>VNĐ</span>
      </div>
      <div v-if="error.salary.status" class="error-label">
        {{ error.salary.text }}
      </div>
    </div>
  </div>
  <div class="button-wrapper">
    <button type="submit" class="button button-finish" @click="onSubmit">
      Hoàn thành
    </button>
    <button type="button" class="button button-back" @click="backStep">
      Quay lại
    </button>
  </div>
</template>
<script>
export default {
  name: "UserWishForm",
  emits: ["backStep", "finishForm"],
  computed: {
    charCount() {
      return this.wishForm.companyReason.length;
    },
  },
  methods: {
    onSubmit() {
      if (!this.wishForm.companyReason.length) {
        this.error.reason = {
          status: true,
          text: "Không được để trống",
        };
      } else if (!this.wishForm.companySalary.length) {
        this.error.salary = {
          status: true,
          text: "Không được để trống",
        };
      } else {
        this.$emit("finishForm", this.wishForm);
      }
    },
    backStep() {
      this.$emit("backStep");
    },
    checkReason() {
      if (!this.wishForm.companyReason.length) {
        this.error.reason = {
          status: true,
          text: "Không được để trống",
        };
      } else if (this.wishForm.companyReason.length > 1000) {
        this.error.reason = {
          status: true,
          text: "Chỉ cho phép độ dài kí tự từ 1-1000",
        };
      } else this.error.reason.status = false;
    },
    checkSalary() {
      const regex = /^\d+$/;
      if (!this.wishForm.companySalary.length) {
        this.error.salary = {
          status: true,
          text: "Không được để trống",
        };
      } else if (!this.wishForm.companySalary.match(regex)) {
        this.error.salary = {
          status: true,
          text: "Chỉ được phép nhập số",
        };
      } else if (this.wishForm.companySalary.length > 10) {
        this.error.salary = {
          status: true,
          text: "Tối đa 10 chữ số",
        };
      } else this.error.salary.status = false;
    },
  },
  data() {
    return {
      wishForm: {
        companyReason: "",
        companySalary: null,
      },
      error: {
        reason: {
          status: false,
          text: "",
        },
        salary: {
          status: false,
          text: "",
        },
      },
    };
  },
};
</script>
<style lang="scss" scoped>
.wish-form {
  width: 926px;
  padding: 20px 32px 24px;
  border: 1px solid #dcdcdc;
  border-radius: 4px;

  &-info {
    width: 528px;
    display: flex;
    flex-direction: column;

    & label {
      font-size: 14px;
      line-height: 20px;
      color: #333333;
      margin-bottom: 6px;

      &::before {
        content: "Must";
        margin-right: 8px;
        color: #ffffff;
        font-weight: 700;
        font-size: 12px;
        line-height: 20px;
        width: 45px;
        height: 20px;
        background: #627d98;
        border-radius: 3px;
        padding: 1px 8px;
      }
    }
  }
}

.company-reason {
  &-textarea {
    resize: none;
    height: 152px;
    outline: none;
    border: 1px solid #dcdcdc;
    border-radius: 4px;
    padding: 8px 10px;
    font-family: "Noto-Sans";
    font-size: 14px;
  }
  &-count {
    font-size: 16px;
    line-height: 24px;
    color: #666666;
  }
}
.salary-wrapper {
  width: 119px;
  height: 40px;
  border: 1px solid #dbdbdb;
  border-radius: 4px;
  display: flex;
  align-items: center;
  padding: 10px 8px;
  justify-content: space-between;
  font-weight: 400;
  font-size: 14px;
  line-height: 20px;
}
.company-salary {
  margin-top: 23px;
  &-input {
    width: 63px;
    outline: none;
    border: none;
  }
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
  &-back {
    background-color: #ffffff;
    border: 1px solid #dcdcdc;
    font-weight: 500;
    color: #666666;
  }
  &-finish {
    width: 142px;
    height: 40px;
    background-color: #627d98;
    font-weight: 700;
    color: #ffffff;
  }
}
.button-wrapper {
  display: flex;
  gap: 26px;
  margin-top: 24px;
}
.error-input {
  border-color: #ed5d5d;
}
.error-label {
  color: #ed5d5d;
  font-size: 14px;
  line-height: 20px;
  margin-top: 10px;
}
.error-count {
  color: #ed5d5d;
}
</style>
