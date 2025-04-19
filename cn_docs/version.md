# 版本管理

-----

## 配置

当版本未被 [静态设置](config/metadata.md#version) 时，应在 `tool.hatch.version` 表中定义相关配置。`source` 选项用于指定 [版本来源](plugins/version-source/reference.md)，以便 [获取](#显示版本) 或 [更新](#更新版本) 版本信息。默认使用的来源是 [regex](plugins/version-source/regex.md)。

使用 `regex` 来源时，必须指定一个 `path` 选项，表示包含项目版本信息的文件的相对路径：

```toml config-example
[tool.hatch.version]
path = "src/hatch_demo/__about__.py"
```

默认的正则模式会查找名为 `__version__` 或 `VERSION` 的变量，其值为一个字符串版本号，可选地以小写字母 `v` 开头。

如果该模式不符合你保存版本的方式，可以使用 `pattern` 选项自定义正则表达式：

```toml config-example
[tool.hatch.version]
path = "pkg/__init__.py"
pattern = "BUILD = 'b(?P<version>[^']+)'"
```

该模式中必须包含一个名为 `version` 的命名分组，以表示版本号的位置。

## 显示版本

调用 [`version`](cli/reference.md#hatch-version) 命令且不带任何参数时，会显示当前项目版本：

```console
$ hatch version
0.0.1
```

## 更新版本

你可以通过以下方式更新版本：

```console
$ hatch version "0.1.0"
Old: 0.0.1
New: 0.1.0
```

`scheme` 选项用于决定用于解析现有版本与新版本的 [版本规则](plugins/version-scheme/reference.md)。默认使用的是 [standard](plugins/version-scheme/standard.md) 规则，该规则基于 [PEP 440](https://peps.python.org/pep-0440/#public-version-identifiers)。

除了显式设置版本号外，还可以指定要递增的 [版本段名](#支持的版本段)：

```console
$ hatch version minor
Old: 0.1.0
New: 0.2.0
```

你也可以使用逗号连接多个段更新。例如，如果你想发布项目第一个主版本的预览版，可以运行：

```console
$ hatch version major,rc
Old: 0.2.0
New: 1.0.0rc0
```

当你准备好发布正式版本时，运行：

```console
$ hatch version release
Old: 1.0.0rc0
New: 1.0.0
```

### 支持的版本段

以下是所有支持的段名称，以及它们对版本 `1.0.0` 的影响：

| 段名称 | 新版本 |
| --- | --- |
| `release` | `1.0.0` |
| `major` | `2.0.0` |
| `minor` | `1.1.0` |
| `micro`<br>`patch`<br>`fix` | `1.0.1` |
| `a`<br>`alpha` | `1.0.0a0` |
| `b`<br>`beta` | `1.0.0b0` |
| `c`<br>`rc`<br>`pre`<br>`preview` | `1.0.0rc0` |
| `r`<br>`rev`<br>`post` | `1.0.0.post0` |
| `dev` | `1.0.0.dev0` |
