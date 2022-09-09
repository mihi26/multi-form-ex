<template>
  <div class="user-form">
    <div class="user-form-info">
      <label for="username" class="user-name">Họ và tên</label>
      <input
        id="username"
        name="username"
        type="text"
        class="user-name-input"
        :class="{ 'error-input': error.username.status }"
        v-model="infoForm.userName"
        @input="checkUserName"
        @blur="checkUserName"
      />
      <div v-if="error.username.status" class="error-label">
        {{ error.username.text }}
      </div>
      <label for="user-dob" class="user-dob">Ngày sinh</label>
      <div class="dob-wrapper">
        <date-picker
          v-model:value="infoForm.userDOB"
          format="YYYY-MM-DD"
          type="date"
          :editable="false"
          prefix-class="xmx"
          class="xmx-datepicker-inline"
          value-type="format"
          @change="checkUserDOB"
        ></date-picker>
      </div>
      <div v-if="error.dob.status" class="error-label">
        {{ error.dob.text }}
      </div>
      <label for="user-city" class="user-city">Thành Phố</label>
      <DropdownComponent
        :listArray="cityList"
        height="40px"
        v-model="infoForm.userCity"
      />
      <label for="user-position" class="user-position">Vị trí làm việc</label>
      <div class="user-position-info">
        Có thể chọn nhiều vị trí mà bạn muốn làm việc.
      </div>
      <TagDropdownComponent
        placeholder="Chọn các vị trí mà bạn muốn"
        :listArray="positionList"
        @updatePosition="updatePosition"
      />
      <label for="user-desc" class="user-desc">Mô tả về bản thân</label>
      <textarea
        id="user-desc"
        class="user-desc-textarea"
        v-model="infoForm.userDesc"
        :class="{ 'error-input': error.desc.status }"
        @input="checkUserDesc"
      />
      <div
        class="user-desc-count"
        :class="{ 'error-count': error.desc.status }"
      >
        {{ charCount }}/1000
      </div>
      <div v-if="error.desc.status" class="error-label">
        {{ error.desc.text }}
      </div>
      <label for="user-avatar-label" class="user-avatar-label"
        >Ảnh cá nhân</label
      >
      <DropzoneComponent
        title="Hãy kéo và thả ảnh vào đây hoặc"
        @updateFile="updateFile"
      />
    </div>
  </div>
  <div class="button-wrapper">
    <button class="button button-next" @click="nextStep">Tiếp</button>
  </div>
</template>
<script>
import axios from "axios";
import DatePicker from "vue-datepicker-next";
import DropzoneComponent from "../components/DropzoneComponent.vue";
import DropdownComponent from "../components/DropdownComponent.vue";
import TagDropdownComponent from "../components/TagDropdownComponent.vue";
import isBefore from "date-fns/isBefore";
import { parseISO, format } from "date-fns";
export default {
  name: "UserInfoForm",
  components: {
    DatePicker,
    DropzoneComponent,
    DropdownComponent,
    TagDropdownComponent,
  },
  emits: ["nextStep", "finishForm"],
  data() {
    return {
      positionList: [
        {
          id: 1,
          name: "Front-end Developer",
        },
        {
          id: 2,
          name: "Back-end Developer",
        },
        {
          id: 3,
          name: "Web Developer",
        },
        {
          id: 4,
          name: "Tester",
        },
      ],
      cityList: [],
      infoForm: {
        userName: "",
        userCity: "Thành phố Hà Nội",
        userPosition: [],
        userDOB: null,
        userDesc: "",
        userAvatar: null,
      },
      error: {
        username: {
          text: "",
          status: false,
        },
        dob: {
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
  methods: {
    fetchApiCity() {
      axios
        .get("https://provinces.open-api.vn/api/")
        .then((res) => (this.cityList = [...res.data]));
    },
    checkUserName() {
      if (!this.infoForm.userName.length) {
        this.error.username = {
          status: true,
          text: "Không được để trống tên",
        };
      } else if (this.infoForm.userName.length > 100) {
        this.error.username = {
          status: true,
          text: "Chỉ cho phép độ dài kí tự từ 1-100",
        };
      } else this.error.username.status = false;
    },
    checkUserDOB() {
      if (!this.infoForm.userDOB) {
        this.error.dob = {
          status: true,
          text: "Không được để trống ngày sinh",
        };
      } else {
        const today = format(new Date(), "yyyy-MM-dd");
        if (!isBefore(parseISO(this.infoForm.userDOB), parseISO(today))) {
          this.error.dob = {
            status: true,
            text: "Không được chọn thời gian quá hiện tại",
          };
        } else this.error.dob.status = false;
      }
    },
    checkUserDesc() {
      if (this.infoForm.userDesc.length > 1000) {
        this.error.desc = {
          status: true,
          text: "Chỉ cho phép độ dài kí tự từ 1-1000",
        };
      } else this.error.desc.status = false;
    },
    nextStep() {
      this.checkUserName();
      this.checkUserDOB();
      this.checkUserDesc();
      if (
        !this.error.username.status &&
        !this.error.dob.status &&
        !this.error.desc.status
      ) {
        this.$emit("finishForm", this.infoForm);
        this.$emit("nextStep");
      }
    },
    updatePosition(selectedPosition) {
      this.infoForm.userPosition = [...selectedPosition];
    },
    updateFile(fileName) {
      this.infoForm.userAvatar = fileName;
    },
  },
  computed: {
    charCount() {
      return this.infoForm.userDesc.length;
    },
  },
  created() {
    this.fetchApiCity();
  },
};
</script>
<style lang="scss" scoped>
.user-form {
  height: auto;
  width: 926px;
  border: 1px solid #dcdcdc;
  border-radius: 4px;

  &-info {
    margin: 20px 0px 0px 40px;
    display: flex;
    flex-direction: column;
    width: 528px;

    & label {
      font-size: 14px;
      line-height: 20px;
      margin-top: 10px;
      margin-bottom: 6px;
      color: #333333;
    }
  }
}
.dob-wrapper {
  width: 118px;
}

.user-name {
  &-input {
    height: 40px;
    padding: 8px 10px;
    border: 1px solid #dcdcdc;
    border-radius: 4px;
    outline: none;
  }
}
.user-name,
.user-dob {
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
.user-city {
  &-select {
    height: 40px;
    padding: 10px;
    border: 1px solid #dbdbdb;
    border-radius: 4px;
    background: #ffffff;
    outline: none;
  }
}
.user-position {
  &-info {
    font-weight: 400;
    font-size: 12px;
    line-height: 20px;
    color: #666666;
    padding-left: 5px;
  }

  &-input {
    outline: none;
    border: none;
    width: 100%;
  }
}
.user-desc {
  &-textarea {
    resize: none;
    height: 152px;
    outline: none;
    border: 1px solid #dbdbdb;
    border-radius: 4px;
    font-family: "Noto Sans";
    padding: 8px 10px;
  }

  &-count {
    font-size: 16px;
    line-height: 24px;
    color: #666666;
  }
}

.user-avatar {
  margin-left: 32px;

  &-label {
    font-weight: 400;
    font-size: 14px;
    line-height: 22px;
  }
}

.button {
  margin-top: 24px;
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
