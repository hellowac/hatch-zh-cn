# 环境收集器插件

-----

环境收集器允许动态修改环境或添加超出配置中定义的环境。用户可以覆盖每个环境提供的默认值。

## 已知的第三方

- [hatch-mkdocs](https://github.com/mkdocs/hatch-mkdocs) - 集成 [MkDocs](https://github.com/mkdocs/mkdocs)，并将依赖推断到环境中

## 安装

任何需要的环境收集器（如果不是内置的）必须与 Hatch 一起手动安装，或者列在 `tool.hatch.env.requires` 数组中，以便自动管理：

```toml config-example
[tool.hatch.env]
requires = [
  "...",
]
```

::: hatch.env.collectors.plugin.interface.EnvironmentCollectorInterface
    options:
      members:
      - PLUGIN_NAME
      - root
      - config
      - get_initial_config
      - finalize_config
      - finalize_environments
