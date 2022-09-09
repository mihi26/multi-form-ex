<script>
import UserInfoForm from "./forms/UserInfoForm.vue";
import UserExperienceForm from "./forms/UserExperienceForm.vue";
import UserWishForm from "./forms/UserWishForm.vue";
export default {
  name: "App",
  data() {
    return {
      step: 1,
      body: {},
    };
  },
  components: { UserInfoForm, UserExperienceForm, UserWishForm },
  methods: {
    nextStep() {
      this.step++;
    },
    backStep() {
      this.step--;
    },
    addForm(form) {
      this.body = Object.assign(this.body, form);
      if (this.step === 3) console.log(this.body);
    },
  },
};
</script>

<template>
  <div class="app">
    <div class="title">Đơn ứng tuyển</div>
    <div class="progress-bar">
      <ul class="progress-bar-list">
        <li class="progress-bar-step" :class="{ active: step === 1 }">
          Thông tin cá nhân
        </li>
        <li class="progress-bar-step" :class="{ active: step === 2 }">
          Kinh nghiệm làm việc
        </li>
        <li class="progress-bar-step" :class="{ active: step === 3 }">
          Về công ty
        </li>
      </ul>
    </div>
    <KeepAlive>
      <UserInfoForm
        v-if="step === 1"
        @nextStep="nextStep"
        @finishForm="addForm"
      />
      <UserExperienceForm
        v-else-if="step === 2"
        @nextStep="nextStep"
        @backStep="backStep"
        @finishForm="addForm"
      />
      <UserWishForm
        v-else-if="step === 3"
        @backStep="backStep"
        @finishForm="addForm"
      />
    </KeepAlive>
  </div>
</template>

<style scoped lang="scss">
.app {
  margin: 160px 0px 252px 254px;
  margin-top: 160px;
  margin-left: 254px;
  font-family: "Noto Sans";
}

.header {
  margin: 16px 0px 20px 0px;
}
.title {
  font-weight: 400;
  font-size: 24px;
  line-height: 36px;
  color: #333333;
}

.progress-bar {
  &-list {
    display: flex;
    list-style-type: none;
    gap: 17px;
    counter-reset: step;
    padding-left: 0px;
    margin: 16px 0px 20px 0px;
  }
  &-step {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
    width: 170px;

    &::before {
      content: counter(step);
      color: #ffffff;
      font-weight: 700;
      font-size: 14px;
      line-height: 20px;
      counter-increment: step;
      display: flex;
      justify-content: center;
      align-items: center;
      width: 32px;
      height: 32px;
      background: #dbdbdb;
      border-radius: 50%;
      margin-bottom: 14px;
    }

    &:first-child::after {
      content: none;
    }

    &::after {
      content: "";
      position: absolute;
      width: 100%;
      height: 1px;
      background: #dbdbdb;
      top: 19px;
      left: -50%;
      z-index: -1;
    }
  }
}
.active {
  &::before {
    background-color: #617d98;
    width: 40px;
    height: 40px;
  }
}
</style>
