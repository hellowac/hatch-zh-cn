# 版本构建钩子（Version build hook）

-----

此构建钩子将项目的版本写入一个文件。

## 配置（Configuration）

构建钩子插件名称为 `version`。

```toml config-example
[tool.hatch.build.hooks.version]
[tool.hatch.build.targets.<TARGET_NAME>.hooks.version]
```

## 选项（Options）

| 选项 | 描述 |
| --- | --- |
| `path`（必选） | 目标文件的相对路径 |
| `template` | 一个字符串，表示 `path` 文件的完整内容，该内容将使用 `version` 变量进行格式化 |
| `pattern` | 不更新整个文件时，可以使用正则表达式，该正则表达式应包含一个名为 `version` 的命名组，表示版本。如果设置为 `true`，则会使用一个模式，查找名为 `__version__` 或 `VERSION` 的变量，该变量设置为包含版本的字符串，版本前可以选择加上小写字母 `v`。 |
