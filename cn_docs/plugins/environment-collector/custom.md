# 自定义环境收集器

-----

这是一个自定义类，位于指定的 Python 文件中，继承自 [EnvironmentCollectorInterface](reference.md#hatch.env.collectors.plugin.interface.EnvironmentCollectorInterface)。

## 配置

环境收集器插件的名称是 `custom`。

```toml config-example
[tool.hatch.env.collectors.custom]
```

## 选项

| 选项     | 默认值          | 描述                                 |
| -------- | --------------- | ------------------------------------ |
| `path`   | `hatch_plugins.py` | Python 文件的路径                    |

## 示例

```python tab="hatch_plugins.py"
    from hatch.env.collectors.plugin.interface import EnvironmentCollectorInterface

    class CustomEnvironmentCollector(EnvironmentCollectorInterface):
        ...
```

如果发现多个子类，必须定义一个名为 `get_environment_collector` 的函数，返回所需的环境收集器。

!!! note
    任何定义的 [PLUGIN_NAME](reference.md#hatch.env.collectors.plugin.interface.EnvironmentCollectorInterface.PLUGIN_NAME) 都会被忽略，并始终为 `custom`。
