## SaaS 图片服务, AJAX 示例

```
<script src="http://code.jquery.com/jquery-3.3.1.min.js" integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8=" crossorigin="anonymous"></script>
<script src="https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.js" type="text/javascript"></script>

<div>
  <input id="images" type='file'  multiple='multiple' accept="image/png,image/jpg,image/jpeg">
  <button onclick="uploadFiles()" >上传</button>
</div>
<div id="imageArea"></div>

<script>
  function uploadFiles() {
    var imgHost1 = "http://47.96.170.148:8084",
        imgHost2 = "https://shengyiplus.idata.mobi/saas_images",
        apiHost = "http://47.96.170.148:8085",
        apiPath = "/api/v1.1/saas/upload/images",
        apiKey  = 'your api key',
        apiToken = md5(apiKey + apiPath + apiKey),
        applicationCode = "test",
        moduleName = 'ajaxNamespace',
        formData = new FormData(),
        inputFiles = document.getElementById("images").files;

    if(!inputFiles.length) {
      alert("请选择文件，支持多选及限制类型为 png/jpg/jepg");
      return false;
    }
    for(var i = 0, len = inputFiles.length; i < len; i ++) {
      formData.append("image" + i, inputFiles[i]); 
    }
    formData.append("api_token", apiToken); 
    formData.append("application_code", applicationCode); 
    formData.append("module_name", moduleName); 
    $.ajax({
        url: apiHost + apiPath,
        type: "post",
        data: formData,
        contentType: false, // 必须false才会自动加上正确的Content-Type
        processData: false, // 必须false才会避开jQuery对 formdata 的默认处理 XMLHttpRequest会对 formdata 进行正确的处理
        success: function (res) {
          if(res.code === 201) {
            var imagePath1, imagePath2;
            for(var i = 0, len = res.data.length; i < len; i ++) {
              imagePath1 = imgHost1 + "/" + applicationCode + "/" + moduleName + "/" + res.data[i];
              imagePath2 = imgHost2 + "/" + applicationCode + "/" + moduleName + "/" + res.data[i];
              $("#imageArea").append("<p>" + imagePath1 + "</p><p>" + imagePath2 + "</p><img style='max-width:300px;' src='" + imagePath2 + "'><br>")
            }
          }
        },
        error: function () {
          alert("上传失败！");
        }
    });
  }
</script>
```

运行效果：

![运行效果](/docs/developer/upload-images-api-examples/run-ajax-example.png)