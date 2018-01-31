## SaaS 图片服务, Vue.js 示例

```
<template>
  <div>
    <el-upload
      multiple
      :limit="9"
      :auto-upload="true"
      :http-request="uploadFiles"
      action=""
      list-type="picture-card" >
      <i class="el-icon-plus"></i>
    </el-upload>

    <p v-show="uploadedImages.length > 0" style="text-align: left;">
      上传成功的图片列表：
    </p>
    <el-table v-show="uploadedImages.length > 0" :data="uploadedImages" style="width: 100%">
      <el-table-column prop="imgSrc" label="预览" width="200">
        <template slot-scope="scope">
          <img :src="scope.row.httpsSrc" style="max-width:200px;">
        </template>
      </el-table-column>
      <el-table-column prop="httpSrc" label="http 链接">
      </el-table-column>
      <el-table-column prop="httpsSrc" label="https 链接">
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import Axios from "axios";
export default {
  data() {
    return {
      uploadedImages: []
    };
  },
  methods: {
    uploadFiles(content){
      let self = this,
          imgHost1 = "http://47.96.170.148:8084",
          imgHost2 = "https://shengyiplus.idata.mobi/saas_images",
          apiHost = "http://47.96.170.148:8085",
          apiPath = "/api/v1.1/saas/upload/images",
          apiKey  = 'your api key',
          apiToken = md5(apiKey + apiPath + apiKey), // https://www.npmjs.com/package/js-md5
          applicationCode = "test",
          moduleName = 'VueNamespace',
          formData = new FormData();

      formData.append("image0", content.file); 
      formData.append("api_token", apiToken); 
      formData.append("application_code", applicationCode); 
      formData.append("module_name", moduleName);
      Axios({
        method: 'post',
        url: apiHost + apiPath,
        data: formData,
        emulateJSON:true
      }).then(function(res){
        res = res.data;
        if(res.code === 201) {
          for(var i = 0, len = res.data.length; i < len; i ++) {
            self.uploadedImages.push({
              httpSrc: imgHost1 + "/" + applicationCode + "/" + moduleName + "/" + res.data[i],
              httpsSrc: imgHost2 + "/" + applicationCode + "/" + moduleName + "/" + res.data[i]
            });
          }
        }
      })
    }
  }
};
</script>
```

运行效果：

![运行效果](/docs/developer/upload-images-api-examples/run-vue-example.png)