# 源码分发构建器（Source distribution builder）

-----

源码分发包（source distribution，简称 `sdist`）是一个包含 Python “源代码” 的归档文件。尽管其结构并无严格规范，但按照惯例，它应包含构建 [wheel](wheel.md) 所需的全部内容，且无需发起网络请求。

## 配置

该构建器插件的名称为 `sdist`。

```toml config-example
[tool.hatch.build.targets.sdist]
```

## 选项（Options）

| 选项 | 默认值 | 描述 |
| --- | --- | --- |
| `core-metadata-version` | `"2.4"` | 所使用的 [核心元数据](https://packaging.python.org/specifications/core-metadata/) 版本 |
| `strict-naming` | `true` | 文件名是否应包含规范化后的项目名称 |
| `support-legacy` | `false` | 是否包含一个 `setup.py` 文件以支持旧式安装机制 |

## 版本类型（Versions）

| 版本 | 描述 |
| --- | --- |
| `standard`（默认） | 最新的常规格式 |

## 默认文件选择

当用户未设置任何 [文件选择](../../config/build.md#file-selection) 选项时，所有未被 [版本控制系统忽略](../../config/build.md#vcs) 的文件将会被包含。

!!! note
    以下文件总是会被包含，且无法排除：

    - `/pyproject.toml`
    - `/hatch.toml`
    - `/hatch_build.py`
    - `/.gitignore` 或 `/.hgignore`
    - 任何已定义的 [`readme`](../../config/metadata.md#readme) 文件
    - 所有已定义的 [`license-files`](../../config/metadata.md#license)

## 可重现性（Reproducibility）

支持 [可重现构建](../../config/build.md#reproducible-builds)。

## 构建数据（Build data）

以下数据可由 [构建钩子](../build-hook/reference.md) 动态修改：

| 数据项 | 默认值 | 描述 |
| --- | --- | --- |
| `dependencies` | | 附加的 [项目依赖](../../config/metadata.md#required) |
