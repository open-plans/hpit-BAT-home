!> 本文档最后更新于 2017年12月9日 如有需要修正，请留言！[@SSDespair](https://github.com/SSDespair)

## 1.hpupload上传组件介绍
厚溥upload(上传组件)是基于js适用于struts2，springmvc等当下流行的各种前端框架上传文件的免费开源web组件；
## 2.组件工作方法,原理,参数
### 2.1原理：通过js将文件上传到后台，并接受后台返回的文件地址
### 2.2方法：
| 方法名 | 描述 |
| --- | --- |
|$.hpUpload() | 单文件上传方法 |
|$.hpUpload_plus.viewUpload | 多文件上传方法 |
### 2.3参数
#### 2.3.1js参数

| 参数名 | 类型  | 属性 | 描述 |
| --- | --- | --- | --- |
| url |http链接    |不能为空 | 文件上传地址     |
| map |map键值对   |可为空   | 文件上传附带参数 |
| json|json |可为空   | 后台返回的数据   |

#### 2.3.2html参数
| 参数名 | 属性 | 描述 |
| --- | --- |--- |
| kdUploadSelect | id | hpupload单图上传默认id |
| kdViewDiv | id |hpupload多图上传默认id|

## 3.组件演示（图片上传）
详细演示请点击如下[链接](http://103.73.49.112:8080/struts2/)查看
## 4.组件快速开始
组件只需要导入*hpUpload.js*，*layer.js*，*jquery*，*hpUpload.css*即可使用，当然你要在后台进行一些接收文件的处理,这里以struts2为例（[组件github链接](https://github.com/hpit-BAT/hpUpload-static-demo)）
### 4.0.1 struts.xml
```
<struts>
     <!-- 后缀名-->
	 <constant name="struts.action.extension" value="hp"></constant>
	 <!-- 最大 上传大小  200m-->
	 <constant name="struts.multipart.maxSize" value="209715200"></constant>
	 <package name="default"  extends="json-default">
	<action name="uploadFile" class="uploadFile.UploadFile" method="uploadFile">
		<result name=""></result>
	  	<result name="ajax" type="json">
				<param name="root">jsonMap</param>
				<param name="contentType">text/html;charset=UTF-8</param>
		</result>
	</action>
	
		<action name="uploadFiles" class="uploadFile.UploadFiles" method="uploadFileList">
		<result name=""></result>
	  	<result name="ajax" type="json">
				<param name="root">jsonMap</param>
				<param name="contentType">text/html;charset=UTF-8</param>
		</result>
	</action>
	 </package>
</struts>   

```
### 4.1单文件
#### 4.1.1前端：
```
<body>
<h1>hpupload上传演示(默认id)</h1>
<img id="imgUrl" />
<button id="kdUploadSelect">按钮上传</button>
</body>
<script type="text/javascript">
$(function() {
//单图url
var url = "http://localhost:8080/struts2/uploadFile.hp";
//传入参数map
var map = {};
map["path"] = "kd/img/";

//  默认id绑定
$.hpUpload(url, map, function(json) {
	console.log(json);
	$("#imgUrl").attr('src', "./" + json.imgPath);
});
});
</script>
```

#### 4.1.2后台：
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
	
	public File getFile() {
		return file;
	}
	public void setFile(File file) {
		this.file = file;
	}
	public String getFileFileName() {
		return fileFileName;
	}
	public void setFileFileName(String fileFileName) {
		this.fileFileName = fileFileName;
	}
	public String getPath() {
		return path;
	}
	public void setPath(String path) {
		this.path = path;
	}
	public Map<String, Object> getJsonMap() {
		return jsonMap;
	}
	public void setJsonMap(Map<String, Object> jsonMap) {
		this.jsonMap = jsonMap;
	}
}
```
![示例](http://upload-images.jianshu.io/upload_images/6733958-88474be1797ff705.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示例](http://upload-images.jianshu.io/upload_images/6733958-52554c4cc01f314e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如上图所示，我们的组件返回一个地址，按照这个地址我们就能回显图片了。

### 4.2多文件
多文件上传组件只是在单文件组件的基础上改下名字 (单文件是*$.hpUpload* ,  多文件是*$.hpUpload_plus.viewUpload*)
如下为多文件上传前端：

#### 4.2.1前端
```
<body>
<h1>hpupload默认id多图上传演示</h1>
<div id="kdViewDiv" class="hp-upload-div"></div>
</body>
<script type="text/javascript">
$(function() {

	//多图url
	var urls = "http://localhost:8080/struts2/uploadFiles.hp";
	//传入参数map
	var map = {};
	map["path"] = "kd/img/";

	//  默认id多图绑定
	$.hpUpload_plus.viewUpload(urls, map, function(json) {
		console.log(json);
	});
});
</script>

```
#### 4.2.1后端
```
public class UploadFiles {
	private InputStream ip = null;
	private OutputStream op = null;
  
	private File []moreFile; // 上传的文件 （多图）
	private String []moreFileFileName; // 上传的文件名称（多图）
	private String path; // 上传的路径 ,如果路径没有,就会创建
	Map<String, Object> jsonMap = new HashMap<String, Object>();
	/**
	 * @功能: 上传文件 如:图片、excel、word.... 单文件上传
	 * @时间: 2017年3月27日
	 * @return
	 */
	public String uploadFileList() {
		List<String> list=new ArrayList<String>();
		for (int i = 0; i < moreFile.length; i++) {
			String imgPath = this.FileUpload(moreFile[i], moreFileFileName[i], path);
			list.add(imgPath);
		}
		//查询进程
		jsonMap.put("imgPath", list);
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

	public File[] getMoreFile() {
		return moreFile;
	}

	public void setMoreFile(File[] moreFile) {
		this.moreFile = moreFile;
	}

	public String[] getMoreFileFileName() {
		return moreFileFileName;
	}

	public void setMoreFileFileName(String[] moreFileFileName) {
		this.moreFileFileName = moreFileFileName;
	}

	public String getPath() {
		return path;
	}

	public void setPath(String path) {
		this.path = path;
	}

	public Map<String, Object> getJsonMap() {
		return jsonMap;
	}

	public void setJsonMap(Map<String, Object> jsonMap) {
		this.jsonMap = jsonMap;
	}
	
}

```

![多图示例](http://upload-images.jianshu.io/upload_images/8587037-6f913e27d2122b80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4.3自定义id（绑定事件（单图）的默认id为 ：*id="kdUploadSelect"* 如果对此id不喜欢可以如下修改自己喜欢的id）
```
<body>
<h1>hpupload自定义元素自定义id上传演示</h1>
<img id="custom_id" src="img/Chrysanthemum.jpg"> 
</body>
 <script type="text/javascript">
$(function() {
//单图url
var url = "http://localhost:8080/struts2/uploadFile.hp";
//传入参数map
var map = {};
map["path"] = "kd/img/";
//  任何元素自定义id绑定
$.hpUpload(url, map, function(json) {
	console.log(json);
	$("#custom_id").attr('src', "./" + json.imgPath);
}, false, "custom_id");
});
  </script>
</html>
```

> 硬盘上传等可以更多的处理图片分离需求，如有需要请[@SSDespair](https://github.com/SSDespair)帮您解答。
