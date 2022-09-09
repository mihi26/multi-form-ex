<template>
  <div
    class="container"
    :class="{ 'border-red-error': error.status, 'border-blue-hover': hover }"
    @drop.prevent="dropHandler($event)"
    @dragover.prevent
    @dragenter.prevent="toggleHover()"
    @dragleave.prevent="toggleHover()"
  >
    <img src="../assets/upload.svg" alt="uploadIcon" class="upload-icon" />
    <div class="title">{{ title }}</div>
    <label for="file-upload" class="file-upload-label"> nhấn vào đây </label>
    <input
      id="file-upload"
      type="file"
      class="file-upload-input"
      @change="addAvatar($event)"
    />
  </div>
  <div v-if="error.status" class="error-label">
    {{ error.text }}
  </div>
  <div class="selected-file" v-if="selectedFile">
    <img src="../assets/file.svg" alt="file_icon" />
    <div class="selected-file-info">
      <div class="selected-file-title">{{ selectedFile.name }}</div>
      <div class="selected-file-size">{{ formatBytes }}</div>
    </div>
    <img
      src="../assets/close.svg"
      alt="close_icon"
      @click="removeFile"
      class="selected-file-remove"
    />
  </div>
</template>
<script>
export default {
  name: "DropzoneComponent",
  data() {
    return {
      selectedFile: null,
      hover: false,
      error: {
        status: false,
        text: "",
      },
    };
  },
  props: {
    title: {
      type: String,
      default: "",
    },
  },
  emits: ["updateFile"],
  methods: {
    checkSize(size) {
      if (size > 10000000) {
        return false;
      }
      return true;
    },
    checkType(type) {
      const imgType = ["image/jpeg", "image/png", "image/svg+xml"];
      if (imgType.includes(type)) {
        return true;
      }
      return false;
    },
    addAvatar(event) {
      console.log(event.target.files[0]);
      if (!this.checkSize(event.target.files[0].size)) {
        this.error = {
          status: true,
          text: "File tối đa 10 MB",
        };
      } else if (!this.checkType(event.target.files[0].type)) {
        this.error = {
          status: true,
          text: "Hãy upload định dạng file jpg,png,svg",
        };
      } else {
        this.error.status = false;
        this.selectedFile = event.target.files[0];
        this.$emit("updateFile", this.selectedFile);
      }
    },
    dropHandler(event) {
      this.hover = false;
      if (!this.checkSize(event.dataTransfer.files[0].size)) {
        this.error = {
          status: true,
          text: "File tối đa 10 MB",
        };
      }
      if (!this.checkType(event.dataTransfer.files[0].type)) {
        this.error = {
          status: true,
          text: "Hãy upload định dạng file jpg, png, svg",
        };
      } else {
        this.error.status = false;
        this.selectedFile = event.dataTransfer.files[0];
        this.$emit("updateFile", this.selectedFile);
      }
    },
    toggleHover() {
      this.hover = !this.hover;
    },
    removeFile() {
      this.selectedFile = null;
      this.$emit("updateFile", this.selectedFile);
    },
  },
  computed: {
    formatBytes() {
      if (this.selectedFile.size === 0) return "0 B";
      const k = 1024;
      const dm = 2;
      const sizes = ["B", "kB", "MB", "GB", "TB", "PB", "EB", "ZB", "YB"];
      const i = Math.floor(Math.log(this.selectedFile.size) / Math.log(k));
      return (
        parseFloat((this.selectedFile.size / Math.pow(k, i)).toFixed(dm)) +
        " " +
        sizes[i]
      );
    },
  },
};
</script>
<style lang="scss" scoped>
.container {
  width: 844px;
  height: 192px;
  text-align: center;
  background-color: #f8f8f8;
  border: 1px solid #dcdcdc;
  border-radius: 7px;
  margin-bottom: 33px;
}
.title {
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  color: #333333;
}
.upload-icon {
  margin-top: 52px;
  margin-bottom: 16px;
}
.file-upload {
  &-label {
    text-decoration: underline;
    cursor: pointer;
  }

  &-input {
    display: none;
  }
}
.border-red-error {
  border-color: #ed5d5d;
}
.border-blue-hover {
  border-color: #185abd;
}

.selected-file {
  display: flex;
  width: 244px;
  height: 48px;
  background: #ffffff;
  border: 1px solid #dcdcdc;
  border-radius: 3px;
  padding: 8px 12px;
  margin-bottom: 53px;
  gap: 12px;

  &-info {
    flex: 1;
  }

  &-title {
    max-width: 145px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    font-size: 12px;
    font-weight: 700;
  }

  &-size {
    font-size: 10px;
  }

  &-remove {
    cursor: pointer;
  }
}
.error-label {
  color: #ed5d5d;
  font-size: 14px;
  line-height: 20px;
  margin-bottom: 10px;
}
</style>
