<template>
  <div class="form-wrapper">
    <div class="exp-form">
      <div class="company-wrapper">
        <DropdownComponent
          height="40px"
          :listArray="listCompany"
          v-model="company.userCompany"
        />
        <img alt="trashcan" src="../assets/trash.svg" @click="removeCompany" />
      </div>
      <div class="job-info-wrapper">
        <label for="job-position" class="user-position">Vị trí từng làm</label>
        <input
          id="job-position"
          name="job-position"
          class="user-position-input"
          :class="{ 'error-input': error.position.status }"
          v-model="company.userPosition"
          @input="checkPosition"
          @blur="checkPosition"
        />
        <div v-if="error.position.status" class="error-label">
          {{ error.position.text }}
        </div>
        <label for="job-date" class="job-date">Thời gian làm việc</label>
        <div class="job-date-wrapper">
          <div class="date-wrapper">
            <date-picker
              v-model:value="company.dateStart"
              format="YYYY-MM-DD"
              type="date"
              :editable="false"
              prefix-class="xmx"
              class="xmx-datepicker-inline"
              value-type="format"
              @change="checkDateStart"
            ></date-picker>
          </div>
          <img alt="calendar" src="../assets/minus.svg" />
          <div class="date-wrapper">
            <date-picker
              v-model:value="company.dateEnd"
              format="YYYY-MM-DD"
              type="date"
              :editable="false"
              prefix-class="xmx"
              class="xmx-datepicker-inline"
              value-type="format"
              @change="checkDateEnd"
            ></date-picker>
          </div>
        </div>
        <div v-if="error.date.status" class="error-label">
          {{ error.date.text }}
        </div>
        <label for="job-desc" class="job-desc">Mô tả về công việc</label>
        <textarea
          id="job-desc"
          name="job-desc"
          class="job-desc-textarea"
          @input="checkDesc"
          v-model="company.jobDesc"
          :class="{ 'error-input': error.desc.status }"
        />
        <div v-if="error.desc.status" class="error-label">
          {{ error.desc.text }}
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import DropdownComponent from "../components/DropdownComponent.vue";
import DatePicker from "vue-datepicker-next";
import isBefore from "date-fns/isBefore";
import { isEqual, parseISO, format } from "date-fns";

export default {
  name: "UserExperienceForm",
  props: {
    company: {
      type: Object,
      required: true,
    },
    listCompany: {
      type: Array,
      required: true,
    },
  },
  components: {
    DatePicker,
    DropdownComponent,
  },
  emits: ["removeCompany"],
  methods: {
    checkPosition() {
      if (!this.company.userPosition.length) {
        this.error.position = {
          status: true,
          text: "Không được để trống",
        };
      } else if (this.company.userPosition.length > 100) {
        this.error.position = {
          status: true,
          text: "Chỉ cho phép độ dài kí tự từ 1-100",
        };
      } else this.error.position.status = false;
    },
    checkDateStart() {
      const today = format(new Date(), "yyyy-MM-dd");
      if (!this.company.dateStart) {
        this.error.date = {
          status: true,
          text: "Không được để trống",
        };
      } else if (!isBefore(parseISO(this.company.dateStart), parseISO(today))) {
        this.error.date = {
          status: true,
          text: "Không được chọn thời gian quá hiện tại",
        };
      } else this.error.date.status = false;
    },
    checkDateEnd() {
      const today = format(new Date(), "yyyy-MM-dd");
      if (!this.company.dateEnd) {
        this.error.date = {
          status: true,
          text: "Không được để trống",
        };
      } else if (!isBefore(parseISO(this.company.dateEnd), parseISO(today))) {
        this.error.date = {
          status: true,
          text: "Không được chọn thời gian quá hiện tại",
        };
      } else this.error.date.status = false;
    },
    checkDesc() {
      if (this.company.jobDesc.length > 5000) {
        this.error.desc = {
          status: true,
          text: "Chỉ cho phép độ dài kí tự từ 1-5000",
        };
      } else this.error.desc.status = false;
    },
    removeCompany() {
      this.$emit("removeCompany", this.company.id);
    },
  },
  data() {
    return {
      error: {
        position: {
          text: "",
          status: false,
        },
        date: {
          text: "",
          status: false,
        },
        desc: {
          text: "",
          status: false,
        },
      },
    };
  },
};
</script>
<style lang="scss" scoped>
.exp-form {
  width: 1026px;
  padding: 24px 24px 38px 24px;
  border: 1px solid #dcdcdc;
  border-radius: 4px;

  & label {
    font-size: 14px;
    line-height: 20px;
    margin-top: 24px;
    margin-bottom: 6px;
    color: #333333;
  }
}
.company-wrapper {
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 10px 16px;
  gap: 16px;
  width: 978px;
  height: 60px;

  background: #f8f8f8;
  border-radius: 4px;
}
.company-select {
  width: 898px;
  height: 40px;
  background: #ffffff;
  border: 1px solid #dbdbdb;
  border-radius: 4px;
  outline: none;
  padding: 8px 10px;
  font-size: 16px;
  line-height: 24px;
  color: #333333;
}

.job-info-wrapper {
  display: flex;
  flex-direction: column;
  width: 528px;
}

img {
  &:hover {
    cursor: pointer;
  }
}
.user-position {
  margin-bottom: 6px;

  &-input {
    height: 40px;
    padding: 8px 10px;
    border: 1px solid #dcdcdc;
    border-radius: 4px;
    outline: none;
    font-size: 16px;
    line-height: 24px;
  }
}
.user-position,
.job-date {
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
.date-wrapper {
  width: 118px;
}
.job-date {
  &-wrapper {
    display: flex;
    flex-direction: row;
    gap: 19px;
  }

  &-input {
    width: 118px;
    height: 40px;
  }
}
.job-desc {
  &-textarea {
    font-family: "Noto Sans";
    font-size: 14px;
    resize: none;
    outline: none;
    background: #ffffff;
    border: 1px solid #dcdcdc;
    border-radius: 4px;
    width: 528px;
    height: 152px;
    padding: 8px 10px;
  }
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
</style>
