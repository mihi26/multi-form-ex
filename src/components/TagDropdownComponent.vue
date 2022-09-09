<template>
  <div class="container">
    <div class="input-wrapper">
      <img src="../assets/search.svg" alt="search" class="search-icon" />
      <div class="tag-container" v-if="selectedItems.length">
        <div v-for="item in selectedItems" class="tag-item">
          {{ item }}
          <img
            src="../assets/times.svg"
            class="tag-icon"
            alt="remove_icon"
            @click="removeTag(item)"
          />
        </div>
      </div>
    </div>
    <input
      name="user-position-info"
      :placeholder="placeholder"
      class="user-position-input"
      id="user-position"
      v-model="searchedString"
      @input="filterList"
    />
    <div class="dropdown" :class="{ active: filteredList.length === 0 }">
      <div
        class="dropdown__item"
        v-for="item in filteredList"
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
  name: "TagDropdownComponent",
  props: {
    placeholder: {
      type: String,
      required: true,
    },
    listArray: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      searchedString: "",
      filteredList: [],
      selectedItems: [],
    };
  },
  methods: {
    filterList() {
      if (this.searchedString.length > 0) {
        this.filteredList = [...this.listArray];
        this.filteredList = this.filteredList.filter((item) =>
          item.name.toLowerCase().includes(this.searchedString.toLowerCase())
        );
      } else this.filteredList = [];
    },
    returnValue(itemName) {
      if (!this.selectedItems.includes(itemName)) {
        this.selectedItems.push(itemName);
      }
      this.$emit("updatePosition", this.selectedItems);
      this.searchedString = "";
      this.filteredList = [];
    },
    removeTag(itemName) {
      this.selectedItems = this.selectedItems.filter(
        (item) => item !== itemName
      );
      this.$emit("updatePosition", this.selectedItems);
    },
  },
};
</script>
<style lang="scss" scoped>
.container {
  position: relative;
  height: auto;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  border: 1px solid #dbdbdb;
  padding: 10px 10px;
  border-radius: 4px;
}
.search-icon {
  margin-right: 8px;
}
.tag {
  &-container {
    display: flex;
    font-family: "Noto-Sans";
    font-size: 14px;
    gap: 4px;
    // max-width: calc(100% - 50px);
    flex-wrap: wrap;
  }

  &-item {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    background: #f0f4f8;
    border: 1px solid #dcdcdc;
    border-radius: 4px;
    padding: 6px 6px;
    gap: 8px;
    color: #627d98;
    white-space: nowrap;
  }

  &-icon {
    width: 14px;
    height: 14px;
    cursor: pointer;
  }
}
.user-position {
  &-input {
    outline: none;
    border: none;
    width: 100%;
    flex: 1;
  }
}
.input-wrapper {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
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
  top: 39px;
  right: 0;
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
