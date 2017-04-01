# webupload+node 实现图片上传图片的bug记录
 web =>> webupload@0.1.1
 <br>
 node =>> express@4.14.0+multer@1.3.0
## 问题
 部分安卓设备无法上传图片(其实就是无法识别图片)
 查看webupload的issues[#185](https://github.com/fex-team/webuploader/issues/185)，上面说设置options `sendAsBinary`=`true`,
 但是我设置后，后端无法正常接收数据。这是multer库已经没有必要了。`bodyParse.raw()`并不能解析webuploader发送过来的二进制，`bodyParse.text()`
 可以解析成字符串但是没什么用，无法保存成图片。于是去google`express`解析二进制流的中间件
 看到这样一篇用来[解析二进制](http://i5ting.github.io/node-http/#10803)的文章,实现了二进制保存成文件，我以为照着issues上说的实现就行，
 但是呵呵还是太年轻，无法上传的设备还是无法上传。还试过[plupload](http://www.plupload.com)一样的问题。
## 原因  
 部分安卓上传图片无法获取image type， 
## 解决
 在使用issues的解答后，无法实现。决定使用原生的，依然无法上传
 <br>
 最终决定使用post base64然后保存成图片，由于使用base64你上传的文件可能有几M或者几M，但是bodyParse.json接收的默认大小是`100kb`，所以需要修改一下
 主要原因是无法获取image的类型，下面的正则兼容了没有image类型的时候的情况
 ```
 var base64Data = body.imgData.replace(/^(data:image\/\w+|data:);base64,/, '')
 var dataBuffer = new Buffer(base64Data, 'base64')
  ```
