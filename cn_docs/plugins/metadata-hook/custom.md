# 自定义元数据钩子（Custom metadata hook）

-----

这是一个在指定 Python 文件中定义的自定义类，该类继承自 [MetadataHookInterface](reference.md#hatchling.metadata.plugin.interface.MetadataHookInterface)。

## 配置（Configuration）

元数据钩子插件名称为 `custom`。

```toml config-example
[tool.hatch.metadata.hooks.custom]
```

## 选项（Options）

| 选项 | 默认值 | 描述 |
| --- | --- | --- |
| `path` | `hatch_build.py` | Python 文件的路径 |

## 示例（Example）

```python tab="hatch_build.py"
from hatchling.metadata.plugin.interface import MetadataHookInterface


class CustomMetadataHook(MetadataHookInterface):
    ...
```

如果找到多个子类，必须定义一个名为 `get_metadata_hook` 的函数，该函数返回所需的元数据钩子。

!!! note
    任何已定义的 [PLUGIN_NAME](reference.md#hatchling.metadata.plugin.interface.MetadataHookInterface.PLUGIN_NAME) 都会被忽略，并始终默认为 `custom`。
