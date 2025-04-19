# 标准版本方案

-----

请参阅 [版本管理](../../version.md#updating) 文档。

## 配置

版本方案插件的名称是 `standard`。

```toml config-example
[tool.hatch.version]
scheme = "standard"
```

## 选项

| 选项 | 描述 |
| ---- | ---- |
| `validate-bump` | 设置特定版本时，确定是否检查新版本是否高于原始版本。默认值为 `true`。 |