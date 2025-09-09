## 更新说明
- 新增 -rm 参数执行删除；
- 支持删除spine资源；
- 支持fnt字体资源；

# AssetCleanerForCocosCreator
**简介：一个基于Node.js的CocosCreator项目资源清理工具，自动化统计有哪些资源未使用，以及各类型资源的占比情况，从而帮助减小包体。**

代码地址：[https://github.com/foupwang/AssetCleanerForCocosCreator](https://github.com/foupwang/AssetCleanerForCocosCreator)

## 为什么需要AssetCleaner
- 随着产品功能增加、版本迭代、需求变更，项目资源越来越臃肿，其中有不少不再使用或未及时删除的资源（不仅仅是图片，还包括序列帧动画、Spine动画、Prefab等等），如何知道哪些资源是可以删除的？一个个手动查找是不能忍受的。
- 产品上线前，优化包体大小是不可避免的问题，包体里究竟有什么资源？哪些资源最占空间？它们的分布比例怎样？
>  想象以下画面：游戏准备上线，你出完版本正要歇口气，老板难以置信的看着你：咱这么点功能，为什么包体有50MB？能减到10MB吗？你：....。现在通过AssetCleaner一键分析后，你可以从容回答：其实代码只占了1MB，剩余49MB是因为UI图片有xxMB、Spine动画有xxMB、音效有xxMB、配置表有xxMB....

- 非动态加载的资源，错误地放到了resources目录，增加网络下载和包体的负担。如何找出它们？
> CocosCreator官方建议：非动态加载的资源不要放在resources目录，否则会增大包体和settings.js大小，且无用资源无法在构建过程中自动剔除。

当项目资源数超过1000个时，以上问题变得更加突出，**`AssetCleaner`为解决以上资源优化问题而生。**
## AssetCleaner功能
- 查找未使用资源。自动查找assets目录下所有未引用的资源，并把结果输出到指定文件，方便开发者核对无误后删除。
- 分析包体。自动统计指定目录下所有文件信息，并按类型区分从大到小输出到指定文件，方便后续分析做重点优化。
- 资源优化。自动统计resources目录下非动态加载的资源，方便开发者核对后移动到非resources目录。

**支持以下文件类型**

  1）.png/.jpg/.webp（包括普通图片、图集、Spine、DragonBone、艺术数字）
  
  2）.prefab/.fire
  
  3）.anim
  
  4）.js/.ts代码

*下图为查找未使用资源的输出示例*
![](https://www.chuyouxiang.com/wp-content/uploads/2019/09/clean.png)

*下图为统计assets原始资源的输出示例*
![](https://www.chuyouxiang.com/wp-content/uploads/2019/09/size1.png)

*下图为统计构建后资源web-mobile的输出示例*
![](https://www.chuyouxiang.com/wp-content/uploads/2019/09/size2.png)
## AssetCleaner使用
`AssetCleaner`基于`Node.js`开发，所以需要先安装`Node.js`，我的测试环境是：`Node.js 10.14.2 + Win10 + CocosCreator 2.1.2`。

目前支持以下几种命令
```
node main.js -clean 项目资源目录 结果输出文件  	// 查找未使用资源
node main.js -clean 项目资源目录 结果输出文件 -d // 查找未使用资源，并且删除项目中未引用的图片、预制件、动画
node main.js -size 项目资源目录 结果输出文件	// 按类型统计目录下所有文件从大到小排序
```
例如，CocosCreator项目路径是`d:/myproject`，则进入AssetCleaner的脚本代码目录。

1）查找未使用资源。命令行输入：
```
node main.js -clean d:/myproject/assets d:/out.txt
```
查找结果将会输出到`d:/out.txt`文件。

2）查找且自动删除未使用资源（谨慎！请确保资源已备份）。命令行输入：
```
node main.js -clean d:/myproject/assets d:/out.txt -d
或者
node main.js -clean d:/myproject/assets d:/out.txt -delete
```
查找结果输出到`d:/out.txt`文件，删除结果输出到`d:/myproject/assets/cleanFiles.txt`文件。

3）按类型统计assets目录下所有原始资源。命令行输入：
```
node main.js -size d:/myproject/assets d:/out.txt
```
查找结果输出到`d:/out.txt`文件。

4）按类型统计构建后的build/web-mobile目录下所有文件。命令行输入：
```
node main.js -size d:/myproject/build/web-mobile d:/out.txt
```
查找结果输出到`d:/out.txt`文件。

## QA
#### 1、`AssetCleaner`会自动删除未使用资源吗？
默认不会，`AssetCleaner`只是分析并把统计结果输出到文件，实际删除需自己手动操作。也可使用可选命令`-d`删除，详见上面说明。
#### 2、`AssetCleaner`为什么不做成Creator插件？
命令行可以更好的结合自动化构建流程，便于拓展。
#### 3、为什么有些资源明明是有用的，但也被认为是未引用资源？
一般是因为资源命名不一致造成的。例如：对于艺术数字类型，如果图片名是font_01.png，则描述文件应是font_01.labelatlas；对于Spine动画，如果图片名是light_01.png，则描述文件应是light_01.atlas和light_01.json；否则该图片可能被认为是未引用。
#### 4、`AssetCleaner`的局限
查找未使用资源的功能，目前主要适用于非resources目录。对于resources目录，因为原则上resources目录只存放动态加载资源，而动态加载的资源名在代码里多数情况下是变量，暂时没找到有效匹配方案，所以目前只是试验性地支持resources目录的.prefab类型（完全匹配）。期待技术大佬们加入这个开源项目，一起完善它。

## 交流
欢迎关注微信公众号“楚游香”，做进一步的技术交流。

![](http://47.104.72.146/wp-content/uploads/2019/09/qrcode_for_gh_d08d74db5b82_258.jpg)
