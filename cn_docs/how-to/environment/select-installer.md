# 如何选择安装器（installer）

-----

## 启用 UV

默认情况下，[virtual](../../plugins/environment/virtual.md) 环境类型使用 [virtualenv](https://github.com/pypa/virtualenv) 创建虚拟环境，并使用 [pip](https://github.com/pypa/pip) 安装依赖项。您可以通过使用 [UV](https://github.com/astral-sh/uv) 替代这两个工具来加速环境创建与依赖解析过程。

!!! warning "注意"
    UV 仍在积极开发中，可能无法处理所有依赖项。

若要启用 UV，可将 `installer` [选项](../../plugins/environment/virtual.md#options) 设置为 `uv`。例如，若希望在 [默认环境](../../config/environment/overview.md#inheritance) 中启用此功能，可设置如下：

```toml config-example
[tool.hatch.envs.default]
installer = "uv"
```

!!! tip
    所有启用 UV 的环境都会通过 `HATCH_UV` 环境变量暴露 UV 的路径。

## 配置 UV 版本

共享的 UV 实例使用的是与 Hatch 兼容的指定版本范围。如果您希望使用不同版本，可覆盖内部 `hatch-uv` 环境的 [dependencies](../../config/environment/overview.md#dependencies) 配置：

```toml config-example
[tool.hatch.envs.hatch-uv]
dependencies = [
  "uv>9000",
]
```

## 外部管理

如果您希望手动管理 UV，可通过设置 `HATCH_ENV_TYPE_VIRTUAL_UV_PATH` 环境变量向 Hatch 指定 UV 的路径（应为绝对路径）。这样将 **隐式启用 UV**（等效于上文中 `installer = "uv"` 的设置）。

## 安装器脚本别名（alias）

如果您在 [scripts](../../config/environment/overview.md#scripts) 或 [commands](../../config/environment/overview.md#commands) 中调用了 `pip` 命令，那么可以将 `uv pip` 命令别名为 `pip`，从而在切换工具时无需更改命令习惯。以下示例展示如何构建一个基于 matrix 的环境，能够 [有条件地](../../config/environment/advanced.md#option-overrides) 启用 UV 并设置别名：

```toml config-example
[[tool.hatch.envs.example.matrix]]
tool = ["uv", "pip"]

[tool.hatch.envs.example.overrides]
matrix.tool.installer = { value = "{matrix:tool}" }
matrix.tool.scripts = [
  { key = "pip", value = "{env:HATCH_UV} pip {args}", if = ["uv"] },
]
```

另一个常见用例是将 UV 暴露给所有 [测试环境](../../config/internal/testing.md)。此时，通常不直接修改 `scripts` 映射，而是添加一个 [extra script](../../config/environment/overview.md#extra-scripts)：

```toml config-example
[tool.hatch.envs.hatch-test.extra-scripts]
pip = "{env:HATCH_UV} pip {args}"
```
