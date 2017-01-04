# electron

###程序运行

```
electron ./
or
electron ./*.js
```

###程序开发

+ 程序入口文件：*.js
+ 主程序入口组件：require('electron')
+ UI程序入口组件：require('electron').remote

> 
主程序

```
{
主程通知组件：const ipcMain = electron.ipcMain;
程序：const app = electron.app; 
窗口：const BrowserWindow = electron.BrowserWindow;
菜单：const Menu = electron.Menu;
托盘：const Tray = electron.Tray;
}
```

> 
UI程序

```
{
新标签：webview
UI通知组件：const ipcRenderer = electron.ipcRenderer;
当前窗口：const currentWindow = remote.getCurrentWindow();
窗口：const browserWindow = remote.require('BrowserWindow');
菜单：const Menu = remote.require('Menu');

}
```

> 互通消息

```
{
mainWindow.webContents.send("playControl", msg);
ipcRenderer.on('playControl', (e, msg)=>{})
}
```

> 平台

```
process.platform 
darwin: OSX
linux: linux

```

> F12 调试

```
// F12打开开发者工具
$(document).bind("keyup", function (event) {
  if (event.keyCode == 123) {
    var currentWindow = remote.getCurrentWindow();

    currentWindow.toggleDevTools();
  }
});
```

> 程序更新

```
const updater = require('electron-updater');
updater.on('ready', function(){
// 好创建窗口 createMainWindow();
})

updater.on('error', function (err) {
});

updater.on('updateRequired', function () {        
// ，程序退出 app.quit();
})

updater.on('updateAvailable', function () {
// 有，负责通知用户
})

var customLogger = {
	log: console.log,
	error: console.error,
	info: console.info,
	warn: console.warn,
	debug: console.debug
};
// 开启更新
updater.start(customLogger)
```

###版本：

```
Node ：process.versions.node
Chromium ：process.versions.chrome
Electron ：process.versions.electron

eg.
We are using node 6.5.0, Chromium 53.0.2785.113, and Electron 1.4.1.
```


```
electron.crashReporter
crashReporter.start({
  productName: 'YourName',
  companyName: 'YourCompany',
  submitURL: 'https://your-domain.com/url-to-submit',
  autoSubmit: true
})
```

```
tray.setToolTip('tootip');
tray.setTitle('title');
tray.getBounds()
tray.displayBalloon({
	icon:nativeImage.createFromPath('./close.png'),
	title:"title",
	content:"content"
});
```

```
1、托盘图标(多图片设置)、托盘提示文字、托盘右键菜单、点击事件
2、系统通知
3、窗口进度条：win.setProgressBar
4、窗口内容加载进度：
win.webContents.session.on('will-download',(e, item, webContents)=>{
	// item.getTotalBytes();
	item.on('updated',()=>{
	// item.getReceivedBytes()
	})
	item.on('done',(e, state)=>{
		if (!win.isDestroyed()) {
            win.setProgressBar(-1);
        }

        if (state === 'interrupted') {

    	} else if(state === 'completed') {

    	}

	})
});
```

###手动下载Eelectron
```
Electron 框架的前身是 Atom Shell,可以让你写使用 JavaScript,HTML 和CSS 构建跨平台的桌面应用程序。它是基于io.js 和 Chromium 开源项目,小编碰到npm安装Electron缓慢网络超时导致失败情况，下面我们来看看。

1. npm源过慢的话，可以把源切到国内的淘宝的镜像上。
npm config set registry https://registry.npm.taobao.org

2. 到electron的国内镜像下载最新的安装包，主要看好自己系统对应的版本
https://npm.taobao.org/mirrors/electron

3. 将下载好的包放到当前用户的根目录下的.electron文件夹下，windows一般是
C:\Users\YourUserName\.electron

4. 执行安装命令npm install electron-prebuilt -g
```


###使用native node模块

```
安装native模块三种方法：

+ electron-rebuild

+ 使用npm

+ 使用node-gyp
```

###测试

+ 测试渲染进程 DevTools

+ 测试主进程 node-inspector 


###简述

+ electron 可以看作一个小的chromium浏览器

+ 在electron中运行package.json和main.js的进程为主进程

+ 主进程通过BrowserWindow创建渲染进程

+ 主进程与渲染进程交互通过 ipcMain和ipcRenderer 监听和发送msg

+ 每个页面拥有自己的渲染进程

+ electron用户通过nodejs的API跨过网页沙箱访问系统应用


### process对象（与node中process区别）

```
process.type 进程的类型可以是browser或renderer
process.version['electron'] electron的版本
process.version['chrome'] Chromium的版本
process.resourcesPath javascript脚本文件的路径
process..mas 构建mac应用商店时为true,其它的为undefined
```

### 支持的Chromium命令行开关
```
--client-certificate=path
--ignore-connections-limit=domains
--disable-http-cache
--remote-debugging-port=port
--js-flags=flags
--proxy-server=address:port
--proxy-bypass-list=hosts
--proxy-pac-url=url
--no-proxy-server
--host-rules=rules
--host-resolver-rules=runles
```

###环境变量
```
export ELECTRON_ENABLE_LOGGING=true
ELECTRON_RUN_AS_NODE
ELECTRON_ENABLE_LOGGING
```

###定制的DOM元素
```
File对象：
DOM的File接口通过HTML5的API提供了用户操作native文件的方法。electron增加了path属性接口可以直接访问文件的真实路径
标签：
webview标签在electron app中植入额外的内容，和iframe不同的是webview运行在一个独立的进程中，它并不具有和你的页面相同的权限，所有app和它的交互都是异步进行的，这使得app在使用植入内容的时候更加安全。
window.open函数：
当调用window.open的时候会在页面中新创建一个窗口，默认情况下创建的BrowserWindow将会继承父类窗口的options。
```

###常用模块

+ 自动更新autoUpdater：提供了Squirrel自动更新框架一个接口
+ contentTracing模块：收集Chromium内容模块产生的跟踪信息
+ dialog模块：显示原生系统对话框，比如打开文件对话框
+ globalShortcut模块：注册和注销全局的键盘快捷方式
+ powerMonitor模块：显示电池电量变化，只能是在主进程中使用
+ powerSaveBlocker模块：阻止系统进入省电模式，休眠模式
+ protocol模块：注册一个定制的协议或是声明使用存在的协议
+ desktopCapturer模块：获取可用的资源，用getUserMedia来获取
+ webContents模块: 负责渲染和控制网页，是BrowserWindow的属性
+ webFrame模块：用来定制当前网页的渲染
+ clipboard模块: 提供了执行复制和粘贴操作的方法
+ crashReporter模块: 发送程序的奔溃报告
+ nativeImage模块: 在electron中获取图片可以通过文件的路径或是使用nativeImage对象
+ screen模块: 保存了关于屏幕的所有相关信息，像是大小，显示，指针位置等等
+ shell模块: 同了和桌面整合相关的函数


###打包

```
用nmp打包成asar:

第一步：安装asar

npm install -g asar
第二步：打包

asar pack your-app app.asar
例如：asar pack F:\atom_project\myatom_1 F:\atom_project\app.asar
这样就会把myatom_1打包成app.asar

electron之Windows下使用html,js,css,开发桌面应用程序_app.rar

一般使用asar()来进行打包，electron中一般包含两种API：
Node.js和Chromium提供的Web API
Node API如fs.readFile
```



###开发笔记

[Electron官网](http://www.yyyweb.com/ctools/demo.php?t=http%3A%2F%2Felectron.atom.io%2F&h=2800&c=&n=electron)

[Electron API](./ElectronAPI.md)

[Electron中文文档一](http://www.kancloud.cn/wizardforcel/electron-doc/137763)

[Electron中文文档二](https://github.com/electron/electron/tree/master/docs-translations/zh-CN)

[w3cSchool Electron教程](http://www.w3cschool.cn/electronmanual/wcx31ql6.html)