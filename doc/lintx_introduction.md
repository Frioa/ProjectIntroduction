#  Lint

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

