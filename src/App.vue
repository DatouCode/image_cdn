<template>
  <div>
    <div class="box" @paste="pasting" contenteditable="true"></div>
    <a :href="url">{{ url }}</a>
  </div>
</template>
<script>
import axios from 'axios'
import {message} from 'ant-design-vue';
import {UploadOutlined} from '@ant-design/icons-vue';
import {defineComponent, onMounted, ref} from 'vue';

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

    const upload = async (str) => {
      let {data} = await axios.post("http://localhost:5000/upload", {
        base64: str
      })
      return data
    }

    const pasting = (e) => {
      // console.log(e.clipboardData.getData("text"))
      let file = e.clipboardData.items[0].getAsFile()
      let reader = new FileReader()
      reader.onload = async (e) => {
        console.log(e.target)
        let result = e.target.result
        url.value = await upload(result)
        console.log(url)
      }
      reader.readAsDataURL(file)
    }

    const fileList = ref([]);
    return {
      url,
      fileList,
      handleChange,
      pasting
    };
  },

});
</script>

<style>
.box {
  width: 500px;
  height: 500px;
  border: 1px solid;
  position: relative;
  float: left;
  overflow: hidden;
}
</style>