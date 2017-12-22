#lib.flexible

移动端自适应方案，相关文章请参考[此处](https://github.com/amfe/article/issues/17)

## Update【2016年01月13日】

首先，由衷的感谢@完颜([@SMbey0nd](https://github.com/SMbey0nd)) 帮忙踩了这个坑，回想起iOS从7~8，从8~9，都踩过只至少一个坑，真的也是醉了。

手淘这边的flexible方案临时升级如下：

- 针对OS 9_3的UA，做临时处理，强制`dpr`为`1`，即`scale`也为`1`，虽然牺牲了这些版本上的高清方案，但是也只能这么处理了
- 这个版本不打算发布到CDN（也不发不到tnpm），所以大家更新的方式，最好手动复制代码内联到`html`中，具体代码可以[点击这里下载](http://www.w3cplus.com/sites/default/files/blogs/2016/1601/flexible.js)

## 最新版本

**0.3.2**

## 用Grunt打包

运行 `npm install`，来安装所需的依赖模块。关于NPM的知识，请参见[nodejs](http://nodejs.org/);

运行 `grunt`，来对项目进行打包。关于Grunt的知识，请参见[gruntjs](http://gruntjs.com/);

## 依赖库

无

完整引用举例：

    <script src="http://g.tbcdn.cn/mtb/lib-flexible/{{version}}/??flexible_css.js,flexible.js"></script>

## 使用方法

建议对于js做`内联处理`，在**所有资源加载之前**执行这个js。

执行这个js后，会在`html`（也就是document.documentElement）上增加一个`data-dpr`属性，以及`font-size`样式。

之后页面中的元素，都可以用rem单位来设置。`html`上的`font-size`就是rem的基准像素。

## 把视觉稿中的px转换成rem

首先，目前视觉稿大小分为`640`，`750`以及，`1125`这三种。

当前方案会把这3类视觉稿分成`100份`来看待（为了以后兼容vh，vw单位）。每一份被称为一个单位a。同时，1rem单位认定为10a。拿750的视觉稿举例：

    1a = 7.5px
    1rem = 75px

因此，对于视觉稿上的元素的尺寸换算，只需要`原始px值`除以`rem基准px值`即可。例如240px * 120px的元素，最后转换为3.2rem * 1.6rem。

## 字体不使用rem的方法

字体的大小不推荐用rem作为单位。所以对于字体的设置，仍旧使用px作为单位，并配合用`data-dpr`属性来区分不同dpr下的的大小。

例如：

    div {
        width: 1rem; 
        height: 0.4rem;
        font-size: 12px; // 默认写上dpr为1的fontSize
    }
    
    [data-dpr="2"] div {
        font-size: 24px;
    }

    [data-dpr="3"] div {
        font-size: 36px;
    }

### 手动配置dpr

引入执行js之前，可以通过输出meta标签方式来手动设置dpr。语法如下：

    <meta name="flexible" content="initial-dpr=2,maximum-dpr=3" />

其中`initial-dpr`会把dpr强制设置为给定的值，`maximum-dpr`会比较系统的dpr和给定的dpr，取最小值。**注意：这两个参数只能选其一。**

### 手动设置rem基准值的方法

输出如下css样式即可：

    html {font-size: 60px!important;}

### 一些常用APIs

**[Number] lib.flexible.dpr**

当前页面的dpr值

**[Number] lib.flexible.rem** 

当前页面的rem基准值

**[Number|String] lib.flexible.rem2px([Number|String digital])**

把rem转换为px

**[Number|String] lib.flexible.px2rem([Number|String digital])** 

把px转换为rem

**lib.flexible.refreshRem()** 

刷新当前页面的rem基准值

## 栅格系统

### 需引入makegrid.js
   
    <script src="http://g.tbcdn.cn/mtb/lib-flexible/{{version}}/makegrid.js"></script>

### 使用方法

    lib.flexible.makeGrid(params)

- [Object params]
    - designWidth - 设计稿宽度
    - designUnit - 设计稿最小单位a（以px为单位）
    - columnCount - 栅格列数
    - columnXUnit - 栅格列宽（以a为单位）
    - gutterXUnit - 栅格间距（以a为单位）
    - edgeXUnit - 页面左右边距（以a为单位）
    - className - 栅格样式的名称（可省略，默认为grid）

通过传入视觉的栅格规范定义，可以输出对应的css样式。

    lib.flexible.makeGridMode(modeName)

- [String modeName]

方案还预置了几个默认的栅格规范，分别是`750-12`，`750-6`，`640-12`，`640-6`。

### 利用meta输出栅格样式

    <meta content="designWidth=750, desginUnit=6, columnCount=12, columnXUnit=7, gutterXUnit=3, edgeXUnit=4" name="grid" />

或

    <meta content="modeName=750-12" name="grid" />

### 栅格代码举例

    <div class="grid">
        <div class="col-4"></div>
        <div class="col-4"></div>
        <div class="col-4"></div>
    </div>

    <div class="grid">
        <div class="col-6"></div>
        <div class="col-6"></div>
    </div>

    <div class="grid">
        <div class="col-3"></div>
        <div class="col-4"></div>
        <div class="col-5"></div>
    </div>


### 四类栅格在不同手机上的表现

![栅格](grid.jpg)

### 在vue项目中使用
 - npm i lib-flexible --save
 - 在项目入口文件main.js中引入 lib-flexible   `import 'lib-flexible'`
 - 在项目根目录的index.html文件中的头部head区域加入手机端适配的meta代码
   + `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
 - 结合px2rem使用 `npm install px2rem --save-dev`
 - 在build文件中的util.js文件中进行配置
   exports.cssLoaders = function (options) {
     .....
    const cssLoader = {
          loader: 'css-loader',
          options: {
            minimize: process.env.NODE_ENV === 'production',
            sourceMap: options.sourceMap
          }
    }
    /*此处新增加px2remLoader*/
    const px2remLoader = {
      loader: 'px2rem-loader',
      options: {
        remUnit: 75
      }
    }
    // generate loader string to be used with extract text plugin
    function generateLoaders (loader, loaderOptions) {
      const loaders = [cssLoader, px2remLoader] // 此处新增px2remLoader进loaders数组
    }

  - 然后重启整个项目，在根元素html和body元素设置一个根字号大小；然后就可以在设置大小的时候直接设置你量取的px数值，浏览器中检查就会看到单位变成rem了。



