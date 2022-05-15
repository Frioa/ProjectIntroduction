# Dart 代码静态分析框架
代码静态分析一般是指在不运行程序的情况下，通过解析源代码、分析代码中存在的编译错误以及代码风格不规范、潜在程序错误等问题。
一般认为代码静态分析能规范代码风格，发现潜在的编程问题，提升工程的质量和可维护性。

## 1. 总体流程

![DDD_layer](https://github.com/Frioa/ProjectIntroduction/blob/master/doc/image/lint-1.png)

这个流程基本就是一个标准的编译前端的工作流程。
以一段代码为例，看看上面的流程：
```
if (Platform.isAndroid) print('hello');
```

### 1.1 Token stream

源代码经过词法分析(lexical analysis)后就得到了 token stream。
Token 相关的 model 类定义在这个文件中：
> dart-lang-sdk/pkg/_fe_analyzer_shared/lib/src/scanner/token.dart

一个简化的 Token 的类关系继承图如下图所示：

![image](https://user-images.githubusercontent.com/33407522/168475420-7dd746bb-28e6-484f-a55d-a6a85c20b425.png)

上面的 source：if (Platform.isAndroid) print('hello'); 所对应的 token stream 大体如下：
- if：{offset: 0, end: 2, length: 2, type: KEYWORD, lexeme: 'if'} 
- (：type: OPEN_PAREN
- Platform：type: IDENTIFIER
- .：type: PERIOD
- ...

### 1.2 Unresolved Syntax tree (AST)
token stream 进行语法解析后就得到一个对应的语法结构(syntactic structure)，也就是我们经常提到的 AST。
在 Dart Analysis 框架中，syntactic structure 的 model 类定义在

> dart-lang-sdk/pkg/analyzer/lib/dart/ast/ast.dart 中。

这些 model 类的类结构简化如下：

![image](https://user-images.githubusercontent.com/33407522/168475386-aed13090-e4f7-48b7-ae1a-bcbdd481404b.png)

### 1.3 Resolved Syntax tree

对语法树 AST 继续进行语义解析就可以得到其语义信息，即代码对应语义结构(semantic structure)。
在 Dart 中，semantic structure 用 element 和 type 来模型化，element 的 model 类定义在：

> dart-lang-sdk/pkg/analyzer/lib/dart/element/element.dart；

例如在 Dart 代码中的 Class 经过语义解析后得到的相关的 element 的 model 类的继承关系如下图所示：

![image](https://user-images.githubusercontent.com/33407522/168475468-2e54e51c-fa9e-458f-a7e4-fc330bb72776.png)



小结
上面的流程几乎就是一个标准的编译前端的工作流程。
Dart Analysis 就是基于上面流程中产生的数据对代码做出分析，查找可能存在的问题。
References
- [The AST](https://github.com/dart-lang/sdk/blob/main/pkg/analyzer/doc/tutorial/ast.md)
- [The Element Model](https://github.com/dart-lang/sdk/blob/main/pkg/analyzer/doc/tutorial/element.md)

## 2. 整体架构

![image](https://user-images.githubusercontent.com/33407522/168475480-bd705a81-6998-468f-94a3-6cf485fd9e08.png)

## 3. Analyze
Analyzer 是整个 Dart Analysis 的核心。代码的解析、分析都是由 Analyzer 完成的。
工作方式：
![image](https://user-images.githubusercontent.com/33407522/168475484-cc4ff72c-254d-40b0-93fc-f920e0aca28a.png)

![image](https://user-images.githubusercontent.com/33407522/168475493-adefc234-2394-438b-a41f-c2de5e3c6f2c.png)

## 3. Analyzer Plugin

如前所述，Analysis Server 还提供了一个plugin framework，方便开发者通过 plugin 的方式给 Analysis Server 提供额外的代码静态检查、代码自动补齐、代码折叠、代码导航、代码高亮、引用查找等能力。

> Analysis Server 负责创建、管理 plugins，并通过约定的协议与之通信。
> Plugin 跟 Analysis Server 运行在同一个 VM 中，但是运行在不同的 Isolate 中。
> Analyzer Plugin的定义
> An analyzer plugin is a piece of code that communicates with the analysis server to provide additional analysis support. The additional support is often specific to > a package or set of packages. 

plugin 本质上就是一段能够让 Analysis Server 找到并执行、以提供相应功能的代码

## package analyzer_plugin

直接实现这些协议是很繁琐的，为了方便开发者开发 plugin，Dart 提供了一个 analyzer_plugin package 来完成这些繁琐无聊的工作。开发者只需 focus 真正的功能开发即可。

如果我们要自己开发一个 plugin 的话，就必须要符合这些约定：
1. 整个约定有四个 package：
  - Target package：要被 analysis 的 package，往往就是我们正在开发的 App；
  - Host package：Bootstrap Package 的宿主 package
  - Bootstrap package： 如其名字，Analysis Server 会运行其中某个 Dart 文件，以启动 plugin
  - Plugin package：真正实现 plugin 功能的 package
2. Target package 在 pubspec 中要依赖 host package；在 analysis_options 中要配置 Host package；
3. Host Package 必须包含一个 tools 目录，tools 目录必须有个名为 analyzer_plugin 的 Bootstrap package；
4. 名为 analyzer_plugin 的 bootstrap package 必须是个真正意义上的 package（能 run pub），且必须存在 bin/plugin.dart，必须包含 main 入口；
5. 一般情况下，Bootstrap package 会在 pubspec 中依赖 Plugin package，main 入口仅仅是个调用 Plugin package 的入口；
6. Plugin package 可以并不是一个独立的 package，例如它可以嵌入在 Host package 中，甚至也可以直接嵌入在 Bootstrap package 中。

