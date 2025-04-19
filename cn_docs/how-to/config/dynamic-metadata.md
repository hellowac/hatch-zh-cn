# 如何配置自定义动态元数据

----

如果您的 [项目元数据](../../config/metadata.md) 不适合以静态方式写入 `pyproject.toml`，则需要通过 [自定义元数据钩子](../../plugins/metadata-hook/custom.md) 在构建时动态提供这些数据。

!!! abstract "替代方案"
    动态元数据是一种在构建时和运行时都可用的“单一真实来源”的实现方式。另一种方式是将构建数据静态写入，并在程序或包中动态查询这些信息，例如使用 [importlib.metadata](https://docs.python.org/3/library/importlib.metadata.html#module-importlib.metadata)。

    如果您关心的仅仅是 [version 字段](../../config/metadata.md#version)，Hatchling 提供了如 [`regex` 版本来源](../../plugins/version-source/regex.md) 这类内置方案，也支持 [第三方插件](../../plugins/version-source/reference.md)。当然，使用本文介绍的方法也可以实现，但相对更复杂。

## 更新项目元数据

修改 `pyproject.toml` 中的 `[project]` 段：

1. 定义 [dynamic 字段](../../config/metadata.md#dynamic)，该字段应是您希望动态设置的所有字段组成的数组，例如：

   ```toml
   dynamic = ["version", "license", "authors", "maintainers"]
   ```

2. 如果上述字段中有任何在 `pyproject.toml` 中已有静态定义，需删除这些静态定义。**不允许同时对字段进行静态和动态定义**。

添加用于加载动态元数据插件的触发段：

```toml
[tool.hatch.metadata.hooks.custom]
```

请使用此名称 **`custom`**，无论您所使用的类名或其 `PLUGIN_NAME` 是什么。该段可以为空。

如果您的插件在执行时需要第三方依赖，请在 `pyproject.toml` 的 `[build-system]` 段的 `requires` 数组中添加这些依赖。

## 实现钩子

动态查找逻辑应通过您自己编写的自定义插件实现。默认期望该插件位于项目根目录下的 `hatch_build.py` 文件中。您需要继承 `MetadataHookInterface` 并实现其 `update()` 方法。例如，以下是一个从 JSON 文件读取元数据的插件：

```python tab="hatch_build.py"
import json
import os

from hatchling.metadata.plugin.interface import MetadataHookInterface


class JSONMetaDataHook(MetadataHookInterface):
    def update(self, metadata):
        src_file = os.path.join(self.root, "gnumeric", ".constants.json")
        with open(src_file) as src:
            constants = json.load(src)
            metadata["version"] = constants["__version__"]
            metadata["license"] = constants["__license__"]
            metadata["authors"] = [
                {"name": constants["__author__"], "email": constants["__author_email__"]},
            ]
```

说明：

1. 必须从 [MetadataHookInterface](../../plugins/metadata-hook/reference.md#hatchling.metadata.plugin.interface.MetadataHookInterface) 导入并继承接口。
2. 所有操作需在 [`update`](../../plugins/metadata-hook/reference.md#hatchling.metadata.plugin.interface.MetadataHookInterface.update) 方法中完成。
3. `metadata` 参数代表 [项目元数据](../../config/metadata.md)。
4. 写入 `metadata` 时，TOML 数组必须使用 Python 的 `list`，即使只有一个元素也必须使用数组格式。
5. TOML 表格字段（如 `authors`）应使用 `dict`。

如果您希望将钩子存储在其他位置，请通过 `path` 选项显式指定其路径：

```toml config-example
[tool.hatch.metadata.hooks.custom]
path = "some/where.py"
```