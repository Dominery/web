# 移动端调试

移动端调试目的是检查网页在移动设备上显示情况，代码功能是否符合预期。

下面将介绍三个移动端调试工具。

### phpstudy

phpstudy用于搭建本地服务器，使移动设备能够访问网页。

需要将网页相关资源放置在phpstudy的www文件夹中。

### Vorlon.js

Vorlon.js是远程调试工具，用于在PC端显示移动设备上的调试信息。

1. 在cmd中输入vorlon，开启vorlon服务
2. 在网址中输入ip地址+vorlon的服务端口，进入vorlon控制页面
3. 在需要进行调试的html页面中插入如`<script src="http://10.21.204.143:1337/vorlon.js"></script>`的标签
4. 手机端访问网页，在PC的vorlon控制页面即可看见调试信息

### Browsersync

browsersync是多终端同步工具，可以监听文件的变动，在文件发生改变时，使所有终端自动刷新页面。

* 进入项目路径的控制台，输入`browser-sync start --server --files="*"`，表示开启服务器，监听所有文件的变动
* browersync服务端口号3000，端口号3001是控制页面