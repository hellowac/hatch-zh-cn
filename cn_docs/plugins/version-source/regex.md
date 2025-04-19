

# 正则版本源

-----

请参阅 [版本控制](../../version.md) 文档。

## 更新

支持设置版本。

## 配置

版本源插件的名称是 `regex`。

```toml config-example
[tool.hatch.version]
source = "regex"
```

## 选项

| 选项 | 描述 |
| ---- | ---- |
| `path`（必填） | 包含项目版本的文件的相对路径 |
| `pattern` | 一个正则表达式，包含一个名为 `version` 的组，代表版本。默认模式查找名为 `__version__` 或 `VERSION` 的变量，并且该变量的值是一个字符串，字符串可能以小写字母 `v` 开头。 |