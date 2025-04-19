# 构建环境配置（Build environment configuration）

-----

你可以完全修改 [`build`](../../cli/reference.md#hatch-build) 命令所使用环境的行为。

## 依赖项（Dependencies）

构建环境始终包含 [构建系统](../build.md#build-system)、[构建目标](../build.md#target-dependencies) 和 [钩子](../build.md#hook-dependencies) 所需的依赖项。

你也可以为构建过程添加其他可能需要的[依赖项](../environment/overview.md#dependencies)：

```toml config-example
[tool.hatch.envs.hatch-build]
dependencies = [
  "cython",
]
```

!!! warning "注意"
    建议仅使用标准机制来定义构建依赖，以便更好地兼容其他工具。

## 环境变量（Environment variables）

你可以定义在构建期间设置的[环境变量](../environment/overview.md#environment-variables)：

```toml config-example
[tool.hatch.envs.hatch-build.env-vars]
SOURCE_DATE_EPOCH = "1580601600"
```

## 安装器（Installer）

默认情况下，[UV 是启用的](../../how-to/environment/select-installer.md)。你可以通过如下方式禁用该行为：

```toml config-example
[tool.hatch.envs.hatch-build]
installer = "pip"
```
