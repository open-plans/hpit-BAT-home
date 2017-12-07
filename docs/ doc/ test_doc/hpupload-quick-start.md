## 1.hpupload上传组件介绍
厚溥upload(上传组件)是基于js适用于struts2，springmvc等当下流行的各种前端框架上传文件的web组件；
## 2.组件工作原理
通过js将文件上传到后台，并接受后台返回json数据
## 3.组件演示（图片上传）
详细演示请点击如下[链接](http://103.73.49.112:8080/struts2/)查看
## 4.组件快速开始
### 1）单文件
组件只需要导入*hpUpload.js*，*layer.js*，*jquery*，*hpUpload.css*即可使用，当然你要在后台进行一些接收文件的处理,这里以struts2为例（绑定元素id为*kdUploadSelect*）
#### 前端：
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<link rel="stylesheet" href="lib/hpUpload/css/hpUpload.css">
<script src="lib/jquery/jquery.min.js"></script>
<script src="lib/layer/layer.js"></script>
<script type="text/javascript" src="lib/hpUpload/hpUpload.js"></script>
<body>
<h1>hpupload上传演示</h1>
<img id="imgUrl"  />
<button id="kdUploadSelect" >上传</button>
</body>
 <script type="text/javascript">
  $(function(){
	  //  按钮绑定
 	  $.hpUpload("uploadFile.hp",{"path":"kd/img/"},function(json){
		  alert(json.imgPath);
		  $("#imgUrl").attr('src',"./"+json.imgPath);
	  }); 
  })
  </script>
</html>
```

#### 后台：
```
public class UploadFile {
	private InputStream ip = null;
	private OutputStream op = null;
  
	private File file; // 上传的文件 （单图）
	private String fileFileName; // 上传的文件名称 （单图）
	private String path; // 上传的路径 ,如果路径没有,就会创建
	Map<String, Object> jsonMap = new HashMap<String, Object>();
	/**
	 * @功能: 上传文件 如:图片、excel、word.... 单文件上传
	 * @作者: 柯栋 @代号:隐无为
	 * @时间: 2017年3月27日
	 * @return
	 */
	public String uploadFile() {
		String imgPath = this.FileUpload(file, fileFileName,path);
		jsonMap.put("imgPath", imgPath);
		return "ajax";
	}
	
	public String FileUpload(File file, String fileFileName, String path) {
		String basePath = ServletActionContext.getServletContext().getRealPath(path);
		// uuid随机名
		String uuidPath = UUID.randomUUID().toString();
		// 文件的后缀名
		String fileExit = getFileExit(fileFileName);//獲取文件後綴名
		// 创建目录
		File dir = new File(basePath);
		if (!dir.exists()) {
			dir.mkdirs();
		}
		// 创建文件的路径
		String newFilePath = basePath + uuidPath + fileExit;
		// 数据库存储的文件相对项目的路径
		String returnName = path+uuidPath + fileExit;
		// 写入文件
		WriteFile(file, newFilePath);
		
		return returnName;
	}
	

	/**
	 * @功能: 获取后缀名
	 * @作者: 柯栋 @代号:隐无为
	 * @时间: 2017年3月27日
	 * @param fileName
	 * @return
	 */
	private String getFileExit(String fileName) {
		int index = fileName.lastIndexOf('.');
		return fileName.substring(index);
	}
	
	/**
	 * @功能: 写入文件
	 * @作者: 柯栋 @代号:隐无为
	 * @时间: 2017年3月27日
	 * @param file 文件
	 * @param path 路径
	 */
	private void WriteFile(File file, String path) {

		try {
			ip = new FileInputStream(file);
			op = new FileOutputStream(path);
			byte[] bytes = new byte[1024];
			int len = 0;
			while ((len = ip.read(bytes)) > 0) {
				op.write(bytes, 0, len);
			}

		} catch (Exception e) {
			// TODO Auto-generated catch block
			throw new RuntimeException();
		} finally {
			if (ip != null) {
				try {
					ip.close();
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				} finally {
					try {
						ip.close();
					} catch (Exception e) {
						throw new RuntimeException(e);
					} finally {
						try {
							op.close();
						} catch (IOException e) {
							throw new RuntimeException(e);
						}
					}
				}
			}
		}
	}
}
```
![示例](http://upload-images.jianshu.io/upload_images/6733958-88474be1797ff705.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示例](http://upload-images.jianshu.io/upload_images/6733958-52554c4cc01f314e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如上图所示，我们的组件返回一个地址，按照这个地址我们就能回显图片了。

### 1）多文件
多文件上传组件只是在单文件组件的基础上改下名字 (单文件是*$.hpUpload* ,  多文件是*$.hpUpload_plus.viewUpload*)
如下为多文件上传前端(后端不用变动)：

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<link rel="stylesheet" href="lib/hpUpload/css/hpUpload.css">
<script src="lib/jquery/jquery.min.js"></script>
<script src="lib/layer/layer.js"></script>
<script type="text/javascript" src="lib/hpUpload/hpUpload.js"></script>
<body>
<h1>hpupload上传演示</h1>
<div id="kdViewDiv"  class="hp-upload-div" ></div>    
</body>
 <script type="text/javascript">
  $(function(){
	  //  按钮绑定
	  $.hpUpload_plus.viewUpload("uploadFiles.hp"path" : "image/"},function(json){
		  console.log(json);
	  });
  });
  </script>
</html>
```

![多图示例](http://upload-images.jianshu.io/upload_images/8587037-6f913e27d2122b80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

tips （绑定事件（多图）的ID必须为 ： id="*kdViewDiv*" 如果对此ID不喜欢可以如下修改自己喜欢的ID）
```
<!DOCTYPE >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<link rel="stylesheet" href="lib/hpUpload/css/hpUpload.css">
<script src="lib/jquery/jquery.min.js"></script>
<script src="lib/layer/layer.js"></script>
<script type="text/javascript" src="lib/hpUpload/hpUpload.js"></script>
<body>
<h1>hpupload上传演示</h1>
<div id="Love-div"  class="hp-upload-div" ></div>    
</body>
 <script type="text/javascript">
  $(function(){
	  //  按钮绑定
	  $.hpUpload_plus.viewUpload("uploadFiles.hp",{"path" : "image/"},function(json){
		  console.log(json);
	  },false,"Love-div");
  });
  </script>
</html>
```
> 硬盘上传可以更多的处理图片分离需求，如有需要请[@我们]()帮您解答。
