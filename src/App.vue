<template>
  <a-upload
      v-model:file-list="fileList"
      name="file"
      action="http://127.0.0.1:5000/upload"
      @change="handleChange"
  >
    <a-button>
      <upload-outlined></upload-outlined>
      Click to Upload
    </a-button>
  </a-upload>
  <br>
  <span>{{ url }}</span>
</template>
<script>
import {message} from 'ant-design-vue';
import {UploadOutlined} from '@ant-design/icons-vue';
import {defineComponent, ref} from 'vue';

export default defineComponent({
  components: {
    UploadOutlined,
  },

  setup() {
    let url = ref("")
    const handleChange = info => {
      if (info.file.status === "removed") {
        url.value = ''
      }
      if (info.file.status !== 'uploading') {
        console.log(info.file, info.fileList);
      }

      if (info.file.status === 'done') {
        message.success(`${info.file.name} file uploaded successfully`);
        url.value = info.file.response.url
      } else if (info.file.status === 'error') {
        message.error(`${info.file.name} file upload failed.`);
      }
    };

    const fileList = ref([]);
    return {
      url,
      fileList,
      handleChange,
    };
  },

});
</script>