# Xcode Linking  "Other Linker Flags"  添加了 $(inherited) 的标签到底有什么作用？



## 先来看看 Xcode buildSetting Linking "Other Linker Flags"的作用

### 编译的基本步骤如下：

 ``` 源代码 > 预处理器 > 编译器 > 汇编器 > 机器码 > 链接器 > 可执行文件```

 "Linking" 是  "链接器 > 可执行文件" 的阶段

链接器，在最后一步需要把.o文件和C语言运行库链接起来，这时候需要用到ld命令。源文件经过一系列处理以后，会生成对应的.obj文件，然后一个项目必然会有许多.obj文件，并且这些文件之间会有各种各样的联系，例如函数调用。链接器做的事就是把这些目标文件和所用的一些库链接在一起形成一个完整的可执行文件

### 我遇到的问题是:

编译时候，某些framework不能被linking的错误，后面同事在 Linking  "Other Linker Flags"  添加了 $(inherited) 的标签

所以问题就是搞明白 这个 $(inherited) 的标签到底是什么意思

字面意思是继承，Google一下，就是Target继承与上层环境的 linking 规则

```
Resolved <- Target <- xcconfig <- Project <- iOS Default
```

上层是 xcconfig，那么xcconfig它又是什么意思

参考这篇文章https://www.jianshu.com/p/aad1f9e72382

介绍了workspace,project和target之间的关系。

项目schema会指定一个target和configuration

在项目中使用了cocoapods的都会发现，在pod项目中有.xcconfig这样的文件，一般有多少个configuration就会有多少个.xcconfig这样的文件。一般工程中就分为debug和release两个

**xcconfig文件就是通过文本方式指定build settings的一种形式**

到这，就知道了这个  $(inherited) 的标签 就是让 Pod库找到一个 .xcconfig文件修改对应的configuration配置文件。

简单理解就是，这个$(inherited)可用于将构建设置从项目级别继承到目标级别。拿添加pod依赖遇到的问题来说就是，当前工程target级别没有继承项目级别的配置，所以获取不到pod中某个库的头文件路径，以致于报错。

project和pod里面的.xcconfig 应该是同个级别的吧！



参考链接：https://stackoverflow.com/questions/15343122/what-is-inherited-in-xcodes-search-path-settings

https://www.jianshu.com/p/aad1f9e72382