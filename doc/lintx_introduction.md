#  Lintx
本文将讲述 lintx 核心功能，因代码在公司的私有库所，将不上传到个人仓库。

## 核心功能
代码静态分析
通过 lintx analyze 可以对项目进行静态分析：

```
$: lintx analyze -h
Analyze your dart codes.

Usage: lintx analyze [arguments] <file>
-h, --help          Print this usage information.
-c, --config        Use configuration from this file.
-o, --option        Use analysis_options.yaml from this file.
    --rules         A list of lint rules to run. For example: avoid_as,annotate_overrides
-l, --linter        Enable lint rules defined in package linter: https://github.com/dart-lang/linter
    --packages      Path to the package resolution configuration file, which
                    supplies a mapping of package names to paths.
-s, --stats         Show lint statistics.
-b, --benchmark     Show lint benchmarks.
-q, --[no-]quiet    Don't show individual lint errors.
    --machine       Print results in a format suitable for parsing.
    --dart-sdk      Custom path to a Dart SDK.
```

## analysis server plugin
lintx 也是一个 analysis server plugin，通过简单配置即可使用：

![微信截图_20220515213831](https://user-images.githubusercontent.com/33407522/168475921-7913aa7c-4979-4dc6-8648-5a2dcabe0d2c.png)

配置生效后，即可在 IDE 中自定检测和 quick fix：

![image](https://user-images.githubusercontent.com/33407522/168475954-c557735c-aac5-4ae1-ac30-de4b43b1d3b6.png)

## 其他功能
除此以外，Lintx 还提供了以下特性：

## ast printer
编写代码静态检查规则前提是了解 ast  的结构，可以使用 lintx 提供的 ast printer 功能来了解代码对应的 AST 的结构。ast printer 将提供的代码转换成 json 格式的 ast：

```
$: dart run lintx ast -h
Print ast structure

Usage: lintx ast [arguments] <file>
-h, --help               Print this usage information.
-s, --segment            Specify the source which matched ast nod will be used as the root when traversal
-d, --depth              Specify the maximum depth (0-based) when traverse the ast.
                           If 'segment' was provided, the tree root will be the matched ast node
-l, --maxSourceLength    Specify the maximum length(100 default) when print source of an Ast node.
                           Source exceeds this limit will be truncated with '...' appending.
                         (defaults to "100")
-r, --[no-]resolve       Resolve the ast
-a, --[no-]matchAll      Print all Ast nodes match source specified by segment
-v, --languageVersion    The dart language version used to determine what set of features will be assumed by the parser
```

## doc
使用 lintx 的 doc 命令可以对编写的自定义规则生成类似 Linter 的 html 在线文档

```
Generate lint rule docs.

Usage: lintx doc arguments
-h, --help    Print this usage information.
-d, --dir     The directory where generated documents host
```
![image](https://user-images.githubusercontent.com/33407522/168476013-d3436122-4094-4b42-941f-0cfafa4afcb7.png)
![image](https://user-images.githubusercontent.com/33407522/168476019-a1835645-752b-4317-8a11-db2d1b08ddc9.png)
![image](https://user-images.githubusercontent.com/33407522/168476029-c273b20e-ba2c-4f82-ae3a-54750d824846.png)

## 支持 quick fix

lintx 的自定义规则框架支持 quick fix 的实现，只需实现 FixComputer 中对应的方法即可：

```
class PreferSingleLineIfExpression extends LintxRule with FixComputer {
  Category get category => Category.style;

  Enforcement get enforcement => Enforcement.trivial;

  @override
  void registerNodeProcessors(NodeLintRegistry registry, LinterContext context) {
    var visitor = _Visitor(this);
    registry.addIfStatement(this, visitor);
  }

  @override
  Future<List<LintFix>> computeFixesForError(AnalysisError error, DartFixesRequest? request) async {
    final ifStatement = metaDataForError(error);
  }
}

class _Visitor extends SimpleAstVisitor<void> {
  @override
  void visitIfStatement(IfStatement ifStmt) {
    rule.reportLint(ifStmt, meta: ifStmt);
  }
}
```

## autofix

lintx 还能对实现了 quick fix 的规则进行自动的修复：

```
Fix lint errors found by linter if possible.

Usage: lintx fix [arguments] <file>
-h, --help          Print this usage information.
-c, --config        Use configuration from this file.
-o, --option        Use analysis_options.yaml from this file.
    --rules         A list of lint rules to fix. For example: avoid_as,annotate_overrides
-l, --linter        Enable lint rules defined in package linter: https://github.com/dart-lang/linter
    --packages      Path to the package resolution configuration file, which
                    supplies a mapping of package names to paths.
-s, --stats         Show lint statistics.
-b, --benchmark     Show lint benchmarks.
-q, --[no-]quiet    Don't show individual lint errors.
    --machine       Print results in a format suitable for parsing.
    --dart-sdk      Custom path to a Dart SDK.
```

## logger
plugin 中的代码由于运行 analysis server 管理的 isolate 中，没有自己的 console，因此如何输入日志是个很麻烦的事情。
lintx 提供了一个 logger，可以通过这个 logger 输出、 查看配置了简单着色的日志：

![image](https://user-images.githubusercontent.com/33407522/168476077-8915b44a-60e7-423d-b6cd-b8dd169be566.png)

## 更多信息
[Dart 代码静态分析框架](https://github.com/Frioa/ProjectIntroduction/blob/master/doc/lint.md)
