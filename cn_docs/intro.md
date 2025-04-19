# 介绍

-----

## 设置项目

可以使用 [`new`](cli/reference.md#hatch-new) 命令将项目设置为可供 Hatch 使用。

### 创建新项目

假设你想创建一个名为 `Hatch Demo` 的项目，你可以运行：

```
hatch new "Hatch Demo"
```

此命令会在你当前的工作目录中创建如下结构：

```
hatch-demo
├── src
│   └── hatch_demo
│       ├── __about__.py
│       └── __init__.py
├── tests
│   └── __init__.py
├── LICENSE.txt
├── README.md
└── pyproject.toml
```

!!! tip
    Hatch 提供了多种方式来自定义项目的生成方式，详见：[自定义项目模板](config/project-templates.md)。

### 初始化现有项目

要初始化一个已有项目，请进入该项目所在目录并运行以下命令：

```
hatch new --init
```

如果你的项目中包含 `setup.py` 文件，该命令将自动迁移其中的 `setuptools` 配置。否则，Hatch 会以交互方式引导你完成设置流程。

## 项目元数据

接下来，你需要在 `pyproject.toml` 文件中定义项目的更多 [元数据](config/metadata.md)。例如你可以指定：

- 项目的 [许可证](config/metadata.md#license)
- 所支持的 [Python 版本](config/metadata.md#python-support)
- 指向文档等项目组成部分的 [相关链接](config/metadata.md#urls)

## 依赖项

设置流程的最后一步是定义项目所需的 [依赖项](config/dependency.md)，以便在初始化时一并包含。

## 配置

Hatch 识别的所有项目特定配置项可以定义在 `pyproject.toml` 文件中，或单独放在名为 `hatch.toml` 的文件中。在 `hatch.toml` 中，配置项不需要放置在 `tool.hatch` 表下：

```toml config-example
[tool.hatch]
option = "..."

[tool.hatch.table1]
option = "..."

[tool.hatch.table2]
option = "..."
```

若在两个文件中均定义了相同配置项，则以 `hatch.toml` 文件中的为准。

!!! tip
    如果你想让配置文件更简洁，可以使用 [点号键](https://toml.io/en/v1.0.0#table)，将上面的配置压缩为如下形式：

    ```toml config-example
    [tool.hatch]
    option = "..."
    table1.option = "..."
    table2.option = "..."
    ```