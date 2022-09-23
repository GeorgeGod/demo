## 借助GitHub的网页在浏览器高速安装ipa包

#### 一、前言

对于iOS的安装包如果想要实现内部分发，可以使用第三方托管服务，如fir或者蒲公英，但是这些服务需要实名认证和一些其他的限制，比较麻烦。所以自己建一个文件下载服务可以解决这些弊端。



#### 二、前提条件

1. 需要安装的设备的UUID已经加入到苹果测试设备列表。
2. nginx开启http服务。(不能是https的服务)
3. 准备一个GitHub的仓库，用于安装网页的展示。



#### 三、开始

1. nginx开启http服务，并将ipa文件放到nginx的`www/files`目录下，没有可以新建。

2. 准备一个`install.plist`文件，并上传到GitHub的仓库中。

   ```plist
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
       <key>items</key>
       <array>
           <dict>
               <key>assets</key>
               <array>
                   <dict>
                       <key>kind</key>
                       <string>software-package</string>
                       <key>url</key>
                       <!-- 需要替换的ipa下载路径 -->
                       <string>http://192.168.1.120/files/a.ipa</string>
                   </dict>
               </array>
               <key>metadata</key>
               <dict>
                   <key>bundle-identifier</key>
                   <!-- 需要替换的bundleID -->
                   <string>com.jarvis.demo</string>
                   <key>kind</key>
                   <string>software</string>
                   <key>title</key>
                   <!-- 需要替换的app名称 -->
                   <string>应用名称</string>
               </dict>
           </dict>
       </array>
   </dict>
   </plist>
   ```



3. 准备一个安装网页`index.html`。

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>安装软件</title>
        <style>
            .content {
                display: flex;
                justify-content: center;
                align-items: center;
            }
            .content a {
                padding: 10px;
                flex-wrap: wrap;
            }
        </style>
    </head>
    <body>
        <div class="content">
          	<!-- 这里替换成install.plist文件目录 -->
            <a href="itms-services://?action=download-manifest&url=https://georgegod.github.io/demo/install.plist">应用名称</a>
        </div>
    </body>
    </html>
    ```



#### 四、开始安装

Safari浏览器打开[安装](https://georgegod.github.io/demo/index.html)链接，就可以下载安装了。

```html
https://georgegod.github.io/demo/index.html
```



#### 五、总结

**由于高版本的iOS系统对安装的链接有更严格的校验，所以安装链接index.html和ipa下载的install.plist文件的路径一定要是HTTPS的，而实际下载的ipa安装包的路径如果是在本地的话必须要是http的。如果ipa和install.plist都在GitHub仓库中，则http和HTTPS都可以下载安装。**

一般来说ipa文件都比较大，为了能高速的下载安装包，所以把ipa放在了本地的服务器上。
