# 配置项目元数据

-----

项目元数据存储在位于项目树根部的 `pyproject.toml` 文件中，完全基于[标准][project metadata standard]。

## 名称 (*required*) ## {: #name }

项目的名称。

```toml tab="pyproject.toml"
[project]
name = "your-app"
```

## 版本号 (*required*) ## {: #version }

=== ":octicons-file-code-16: pyproject.toml"

    === "Dynamic"
        请参阅专门的 [版本管理](../version.md) 部分。

        ```toml
        [project]
        ...
        dynamic = ["version"]

        [tool.hatch.version]
        path = "..."
        ```

    === "Static"
        ```toml
        [project]
        ...
        version = "0.0.1"
        ```

## 描述

该项目的简要概述。

```toml tab="pyproject.toml"
[project]
...
description = '...'
```

## Readme

项目的完整描述。

=== ":octicons-file-code-16: pyproject.toml"

    === "Simple"
        文件扩展名必须是“.md”、“.rst”或“.txt”。

        ```toml
        [project]
        ...
        readme = "README.md"
        ```

    === "Complex"
        `content-type` 字段必须设置为 `text/markdown`、`text/x-rst` 或 `text/plain`。

        === "File"
            还可以设置“charset”字段来指示使用哪种编码来读取文件，默认为“utf-8”。

            ```toml
            [project]
            ...
            readme = {"file" = "README.md", "content-type" = "text/markdown"}
            ```

        === "Text"
            `content-type` 字段必须设置为 `text/markdown` 或 `text/x-rst`。

            ```toml
            [project]
            ...
            readme = {"text" = "...", "content-type" = "text/markdown"}
            ```

!!! note
    如果将其定义为文件，则它将始终包含在[源分发](../plugins/builder/sdist.md)中以实现一致的构建。

## Python 支持

项目的Python版本要求。

```toml tab="pyproject.toml"
[project]
...
requires-python = ">=3.8"
```

## 许可

更多信息请参阅[PEP 639][]。

=== ":octicons-file-code-16: pyproject.toml"

    === "SPDX expression"

        ```toml
        [project]
        ...
        license = "Apache-2.0 OR MIT"
        ```

    === "Files"

        ```toml
        [project]
        ...
        license-files = ["LICENSES/*"]
        ```

## 所有权

被视为项目“作者”或“维护者”的个人或组织。其确切含义尚有待进一步解释；它可能列出软件包的原始或主要作者、当前维护者或所有者。如果值相同，则仅使用“authors”字段。

```toml tab="pyproject.toml"
[project]
...
authors = [
  { name = "...", email = "..." },
]
maintainers = [
  { name = "...", email = "..." },
]
```

## 关键字

用于协助发现项目的关键词。

```toml tab="pyproject.toml"
[project]
...
keywords = [
  "...",
]
```

## 分类

适用于项目的 [trove 分类器](https://pypi.org/classifiers/)。

```toml tab="pyproject.toml"
[project]
...
classifiers = [
  "...",
]
```

## URLs

URL 表，其中键是 URL 标签，值是 URL 本身。

```toml tab="pyproject.toml"
[project.urls]
Documentation = "..."
"Source code" = "..."
```

## 依赖项

如需了解更多信息，请参阅[依赖项规范](dependency.md)页面。

条目默认支持[上下文格式](context.md)和[禁止直接引用](#allowing-direct-references)。

### 必须的

```toml tab="pyproject.toml"
[project]
...
dependencies = [
  "...",
]
```

### 可选的

```toml tab="pyproject.toml"
[project.optional-dependencies]
option1 = [
  "...",
]
option2 = [
  "...",
]
```

## 入口点

[入口点](https://packaging.python.org/specifications/entry-points/) 是项目用来宣传其提供的组件以供其他代码发现和使用的一种机制。

### CLI

安装定义 CLI 脚本的项目后，每个键都将沿着“PATH”作为调用其关联对象的命令提供。

```toml tab="pyproject.toml"
[project.scripts]
cli-name = "pkg.subpkg:func"
```

使用上面的例子，运行 `cli-name` 基本上会执行以下 Python 脚本：

```python
import sys

from pkg.subpkg import func

sys.exit(func())
```

### GUI

GUI 脚本与 CLI 脚本完全相同，但 Windows 除外，在 Windows 上它们经过特殊处理，因此无需控制台即可启动。

```toml tab="pyproject.toml"
[project.gui-scripts]
gui-name = "pkg.subpkg:func"
```

### 插件

```toml tab="pyproject.toml"
[project.entry-points.plugin-namespace]
plugin-name1 = "pkg.subpkg1"
plugin-name2 = "pkg.subpkg2:func"
```

## 动态的

如果任何元数据字段是动态设置的，例如 [`version`](#version)，那么它们必须在此处列出。

```toml tab="pyproject.toml"
[project]
...
dynamic = [
  "...",
]
```

## 元数据选项

### 允许直接引用

默认情况下，[依赖项](#dependencies) 不允许定义 [直接引用](https://peps.python.org/pep-0440/#direct-references)。要禁用此检查，请将 `allow-direct-references` 设置为 `true`：

```toml config-example
[tool.hatch.metadata]
allow-direct-references = true
```

### 允许模棱两可的特征

默认情况下，[可选依赖项](#optional) 的名称会被规范化以避免出现歧义。要禁用此规范化，请将 `allow-ambiguous-features` 设置为 `true`：

```toml config-example
[tool.hatch.metadata]
allow-ambiguous-features = true
```

!!! danger "已弃用"
    此选项暂时存在，以便与尚不支持 [PEP 685](https://peps.python.org/pep-0685/) 的工具提供更好的互操作性，并将在 2024 年 1 月 1 日之后的第一个次要版本中被删除。
