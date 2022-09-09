<template lang="">
  <!-- <select :style="style" @change="returnValue" :value="modelValue">
    <option v-for="item in listArray" :value="item?.name" :key="item?.id">
      {{ item?.name }}
    </option>
  </select> -->
  <div class="container" :style="style">
    <div class="input-wrapper" @click="toggleDropdown">
      <input type="text" :style="style" readonly :value="modelValue" />
      <img alt="select-arrow" src="../assets/selectDown.svg" />
    </div>
    <div class="dropdown" :class="{ active: !toggle }">
      <div
        class="dropdown__item"
        v-for="item in listArray"
        :key="item?.id"
        @click="returnValue(item?.name)"
      >
        {{ item?.name }}
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "DropdownComponent",
  props: {
    modelValue: {
      type: String,
    },
    listArray: {
      type: Array,
      required: true,
    },
    width: {
      type: String,
      default: "100%",
    },
    height: {
      type: String,
      default: "auto",
    },
  },
  methods: {
    toggleDropdown() {
      this.toggle = !this.toggle;
    },
    returnValue(itemValue) {
      this.$emit("update:modelValue", itemValue);
      this.toggle = !this.toggle;
    },
  },
  computed: {
    style() {
      return { width: this.width, height: this.height };
    },
  },
  data() {
    return {
      toggle: false,
    };
  },
};
</script>
<style lang="scss" scoped>
.container {
  position: relative;
}
img {
  position: absolute;
  top: 12px;
  right: 12px;
}
input {
  font-family: "Noto Sans";
  outline: none;
  border: 1px solid #dcdcdc;
  border-radius: 4px;
  padding: 8px 10px;
  font-size: 14px;
  cursor: pointer;
  position: relative;
}
.input-wrapper {
  position: relative;
  cursor: pointer;
}

.dropdown {
  display: flex;
  position: absolute;
  flex-direction: column;
  margin-left: auto;
  margin-right: auto;
  width: 100%;
  height: 117px;
  overflow: auto;
  z-index: 100;
  &__item {
    font-size: 14px;
    text-align: left;
    padding: 10px;
    gap: 10px;
    color: #486581;
    background-color: #f1f5f8;
    &:last-child {
      border-radius: 0px 0px 4px 4px;
    }
    &:hover {
      cursor: pointer;
      color: #ffffff;
      background-color: #617d98;
    }
  }
}
.active {
  display: none;
}
</style>
