<template>
  <div class="upload_form clearfix">
    <h2>Поиск объектов в видео</h2>
    <a-upload class="upload_form" accept="video/*" :file-list="fileList" :remove="handleRemove" :before-upload="beforeUpload">
      <a-button> <a-icon type="upload" /> Выбрать файл </a-button>
    </a-upload>
    <a-button
      type="primary"
      :disabled="fileList.length === 0"
      :loading="uploading"
      style="margin-top: 16px"
      @click="handleUpload"
    >
      {{ uploading ? 'Загрузка и обработка' : 'Загрузить' }}
    </a-button>
    <h2 style="margin-top: 20px" v-if="mlData && mlData.accuracy && mlData.label">Обнаружено</h2>
    <div class="upload_form__tags" v-if="mlData && mlData.accuracy && mlData.label">
      <a-tag class="upload_form__tag" color="blue" v-for="(el, i) in mlData.label" :key="i">
        {{ el }} ({{ mlData.accuracy[i] }})
      </a-tag>
    </div>
  </div>
</template>
<script>
export default {
  name: 'UploadVideo',
  data () {
    return {
      fileList: [],
      uploading: false,
      mlData: null
    }
  },
  methods: {
    handleRemove (file) {
      const index = this.fileList.indexOf(file)
      const newFileList = this.fileList.slice()
      newFileList.splice(index, 1)
      this.fileList = newFileList
    },
    beforeUpload (file) {
      this.fileList = [file]
      return false
    },
    async handleUpload () {
      const { fileList } = this
      const formData = new FormData()
      fileList.forEach((file) => {
        formData.append('file', file)
      })
      this.uploading = true
      this.mlData = null

      await this.$axios.$post('/api/', formData)
        .then((res) => {
          this.fileList = []
          this.uploading = false
          this.$message.success('Обработка завершена')
          this.mlData = res
        })
        .catch(() => {
          this.uploading = false
          this.$message.error('Ошибка при загрузке')
        })
    }
  }
}
</script>

<style>
.upload_form {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.upload_form__tags {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  max-width: 400px;
}

.upload_form__tag {
  margin-bottom: 8px;
}
</style>
