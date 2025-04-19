# 自定义构建器（Custom builder）

-----

这是一个自定义类，位于给定的 Python 文件中，继承自 [BuilderInterface](reference.md#hatchling.builders.plugin.interface.BuilderInterface)。

## 配置

该构建器插件的名称为 `custom`。

```toml config-example
[tool.hatch.build.targets.custom]
```

## 选项（Options）

| 选项 | 默认值 | 描述 |
| --- | --- | --- |
| `path` | `hatch_build.py` | Python 文件的路径 |

## 示例（Example）

```python tab="hatch_build.py"
from hatchling.builders.plugin.interface import BuilderInterface


class CustomBuilder(BuilderInterface):
    ...
```

如果发现多个子类，必须定义一个名为 `get_builder` 的函数，返回所需的构建器。

!!! note
    任何已定义的 [PLUGIN_NAME](reference.md#hatchling.builders.plugin.interface.BuilderInterface.PLUGIN_NAME) 都会被忽略，且始终为 `custom`。
