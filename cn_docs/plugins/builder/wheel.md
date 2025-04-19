# Wheel 构建器

-----

[Wheel](https://packaging.python.org/specifications/binary-distribution-format/) 是 Python 包的一种二进制分发格式，可以直接安装到环境中。

## 配置

该构建器插件的名称为 `wheel`。

```toml config-example
[tool.hatch.build.targets.wheel]
```

## 选项（Options）

| 选项 | 默认值 | 描述 |
| --- | --- | --- |
| `core-metadata-version` | `"2.4"` | 所使用的 [核心元数据](https://packaging.python.org/specifications/core-metadata/) 版本 |
| `shared-data` | | 一个映射，类似于 [强制包含](../../config/build.md#forced-inclusion) 选项，指定标准 [数据目录](https://packaging.python.org/en/latest/specifications/binary-distribution-format/#the-data-directory) 中 `data` 子目录的内容，将在给定的 Python 环境中全局安装，通常位于 `#!python sys.prefix` 路径下 |
| `shared-scripts` | | 一个映射，类似于 [强制包含](../../config/build.md#forced-inclusion) 选项，指定标准数据目录中 `scripts` 子目录的内容，将在给定的 Python 环境中安装，通常在 Windows 下位于 `Scripts`，其他平台为 `bin`，且通常会位于 PATH 中 |
| `extra-metadata` | | 一个映射，类似于 [强制包含](../../config/build.md#forced-inclusion) 选项，指定将以 `extra_metadata` 目录形式附带的额外 [元数据](https://peps.python.org/pep-0427/#the-dist-info-directory) |
| `strict-naming` | `true` | 文件名是否应包含规范化后的项目名称 |
| `macos-max-compat` | `false` | 在 macOS 上，当构建钩子设置了 `infer_tag` [构建数据](#build-data) 时，wheel 名称是否应指示对新 SDK 版本的广泛支持，而非特定版本。<br><br>注意：该选项未来将被移除。 |
| `bypass-selection` | `false` | 当用户未定义任何文件选择选项，且所有启发式方法均失败时，是否抑制抛出的错误 |

## 版本类型（Versions）

| 版本 | 描述 |
| --- | --- |
| `standard`（默认） | 最新的标准格式 |
| `editable` | 仅打包 `.pth` 文件或导入钩子的 wheel，支持实时开发 |

## 默认文件选择

当用户未设置任何 [文件选择](../../config/build.md#file-selection) 选项时，将根据 [项目名称](../../config/metadata.md#name) 按以下启发顺序确定要打包的模块：

1. `<NAME>/__init__.py`
2. `src/<NAME>/__init__.py`
3. `<NAME>.py`
4. `<NAMESPACE>/<NAME>/__init__.py`

若以上条件均不满足，则会抛出错误。

## 可重现性（Reproducibility）

支持 [可重现构建](../../config/build.md#reproducible-builds)。

## 构建数据（Build data）

以下数据可由 [构建钩子](../build-hook/reference.md) 动态修改：

| 数据项 | 默认值 | 描述 |
| --- | --- | --- |
| `tag` | | 文件名中的完整 [tag](https://peps.python.org/pep-0425/)（例如 `py3-none-any`），默认为跨平台 wheel，基于 [项目元数据](../../config/metadata.md#python-support) 中支持的 Python 主版本生成 |
| `infer_tag` | `#!python False` | 当未设置 `tag` 时，可启用该项以根据平台、解释器和 ABI 选择最具体的 tag |
| `pure_python` | `#!python True` | 是否写入标识纯 Python 包的元数据（即无平台特定文件） |
| `dependencies` | | 附加的 [项目依赖](../../config/metadata.md#required) |
| `shared_data` | | 附加的 [`shared-data`](#options) 项，若有冲突将优先生效 |
| `shared_scripts` | | 附加的 [`shared-scripts`](#options) 项，若有冲突将优先生效 |
| `extra_metadata` | | 附加的 [`extra-metadata`](#options) 项，若有冲突将优先生效 |
| `force_include_editable` | | 类似于 [`force_include` 选项](../build-hook/reference.md#build-data)，但专用于 `editable` [版本](#versions)，且具有更高优先级 |
