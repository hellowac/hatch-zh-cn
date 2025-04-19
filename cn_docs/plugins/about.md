# 插件（Plugins）

-----

Hatch 使用 [pluggy](https://github.com/pytest-dev/pluggy) 来实现插件功能。

## 概览（Overview）

所有插件都通过注册钩子（registration hooks）提供一个或多个继承自特定 [类型接口](#types) 的类。

每个注册钩子都必须由 Hatch 的钩子标记装饰。例如，如果你想创建一种新的环境类型，可以这样写：

```python tab="hooks.py"
from hatchling.plugin import hookimpl

from .plugin import SpecialEnvironment


@hookimpl
def hatch_register_environment():
    return SpecialEnvironment
```

钩子函数可以返回单个类，也可以返回类的列表。

每个类都必须定义一个名为 `PLUGIN_NAME` 的属性，用户在选择插件时将使用该名称。因此在上例中，该类可能定义如下：

```python tab="plugin.py"
...
class SpecialEnvironment(...):
    PLUGIN_NAME = 'special'
    ...
```

## 项目配置（Project configuration）

### 命名规范（Naming）

建议插件项目名称以 `hatch-` 为前缀。例如，若你为名为 `foo` 的产品编写插件，可定义为：

```toml tab="pyproject.toml"
[project]
name = "hatch-foo"
```

### 插件发现（Discovery）

你需要将项目定义为 Hatch 的一个 [Python 插件](../config/metadata.md#plugins)：

```toml tab="pyproject.toml"
[project.entry-points.hatch]
foo = "pkg.hooks"
```

插件名称应为项目名称去掉 `hatch-` 前缀后的部分，而路径应指向包含注册钩子的模块。

### 分类器（Classifier）

将 [`Framework :: Hatch`](https://pypi.org/search/?c=Framework+%3A%3A+Hatch) 添加到项目的 [分类器](../config/metadata.md#classifiers) 中，以便用户更容易搜索到 Hatch 插件：

```toml tab="pyproject.toml"
[project]
classifiers = [
  ...
  "Framework :: Hatch",
  ...
]
```

## 插件类型（Types）

### Hatchling 插件

这些插件用于构建项目，因此定义的依赖项会自动安装在每个构建环境中：

- [构建器 Builder](builder/reference.md)
- [构建钩子 Build hook](build-hook/reference.md)
- [元数据钩子 Metadata hook](metadata-hook/reference.md)
- [版本来源 Version source](version-source/reference.md)
- [版本方案 Version scheme](version-scheme/reference.md)

### Hatch 插件

这些插件必须安装在与 Hatch 本身相同的环境中：

- [环境 Environment](environment/reference.md)
- [环境收集器 Environment collector](environment-collector/reference.md)
- [发布器 Publisher](publisher/reference.md)
