### 环境配置

-----

所有环境都在 `tool.hatch.envs` 表中定义为不同的部分。

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
```

环境的[存储位置](../hatch.md#environments)是完全可配置的。

除非在命令行中显式选择了一个环境，否则将使用 `default` 环境。此环境的[类型](#type)默认是 `virtual`。

!!! info
    以 `hatch-` 为前缀的环境用于特殊目的，例如[测试](../internal/testing.md)。

## 继承

所有环境都继承自其 `template` 选项定义的环境，默认值为 `default`。

例如，以下配置：

```toml config-example
[tool.hatch.envs.foo]
type = "baz"
skip-install = true

[tool.hatch.envs.bar]
template = "foo"
skip-install = false
```

`bar` 环境将是 `baz` 类型，并且 `skip-install` 设置为 `false`。

!!! note
    环境不继承[矩阵](advanced.md#matrix)。

### 自引用环境

您可以通过将 `template` 设置为环境自身的名称来禁用继承：

```toml config-example
[tool.hatch.envs.foo]
template = "foo"
```

### 独立环境

一个常见的用例是独立环境，它们不需要继承或安装项目，例如用于代码检查或有时用于构建文档。启用 `detached` 选项将使环境[自引用](#self-referential-environments)，并将[跳过项目安装](#skip-install)：

```toml config-example
[tool.hatch.envs.lint]
detached = true
```

## 依赖项

除了项目元数据中定义的依赖项外，您还可以安装[其他依赖项](../dependency.md)。条目支持[上下文格式化](advanced.md#context-formatting)。

```toml config-example
[tool.hatch.envs.test]
dependencies = [
  "coverage[toml]",
  "pytest",
  "pytest-cov",
  "pytest-mock",
]
```

如果您定义的环境与它们的[继承环境](#inheritance)的依赖项仅有少许不同，您可以使用 `extra-dependencies` 选项来避免重新声明 `dependencies` 选项：

```toml config-example
[tool.hatch.envs.default]
dependencies = [
  "foo",
  "bar",
]

[tool.hatch.envs.experimental]
extra-dependencies = [
  "baz",
]
```

!!! tip
    Hatch 使用[pip](https://github.com/pypa/pip)来安装依赖项，因此它也支持任何[pip 配置](https://pip.pypa.io/en/stable/topics/configuration/)。例如，如果您只想使用私人仓库，可以设置 `PIP_INDEX_URL` [环境变量](#environment-variables)。

## 安装

### 特性（可选依赖） ### {: #features }

如果您的项目定义了[可选依赖](../metadata.md#optional)，可以使用 `features` 选项选择要安装的组：

```toml config-example
[tool.hatch.envs.nightly]
features = [
  "server",
  "grpc",
]
```

!!! note
    特性/可选依赖在其他工具中也称为 `extras`。

### 开发模式

默认情况下，环境总是反映您项目在磁盘上的当前状态，例如，通过在 Python 环境中以可编辑模式安装它。设置 `dev-mode` 为 `false` 可以禁用此行为，只有在创建新环境时才会安装您的项目。此后，您需要手动管理项目安装。

```toml config-example
[tool.hatch.envs.static]
dev-mode = false
```

### 跳过安装

默认情况下，环境会在创建时安装您的项目。要忽略此步骤，可以将 `skip-install` 设置为 `true`：

```toml config-example
[tool.hatch.envs.lint]
skip-install = true
```

## 环境变量

### 定义的环境变量

您可以通过 `env-vars` 选项定义环境变量：

```toml config-example
[tool.hatch.envs.docs]
dependencies = [
  "mkdocs"
]
[tool.hatch.envs.docs.env-vars]
SOURCE_DATE_EPOCH = "1580601600"
```

值支持[上下文格式化](advanced.md#context-formatting)。

### 过滤器

默认情况下，环境将访问所有环境变量。您可以使用 `env-include`/`env-exclude` 选项通过通配符模式进行过滤：

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
env-include = [
  "FOO*",
]
env-exclude = [
  "BAR",
]
```

排除模式具有优先级，但永远不会影响[定义的](#defined)环境变量。

## 脚本

您可以定义命名脚本，这些脚本可以在[执行](../../environment.md#command-execution)时调用，或者在其他脚本的开头引用。[上下文格式化](advanced.md#context-formatting)也得到了支持。

例如，在以下配置中：

```toml config-example
[tool.hatch.envs.test]
dependencies = [
  "coverage[toml]",
  "pytest",
  "pytest-cov",
  "pytest-mock",
]
[tool.hatch.envs.test.scripts]
run-coverage = "pytest --cov-config=pyproject.toml --cov=pkg --cov=tests"
run = "run-coverage --no-cov"
```

`run` 脚本将展开为：

```
pytest --cov-config=pyproject.toml --cov=pkg --cov=tests --no-cov
```

脚本也可以定义为字符串数组。

```toml config-example
[tool.hatch.envs.style]
detached = true
dependencies = [
  "flake8",
  "black",
  "isort",
]
[tool.hatch.envs.style.scripts]
check = [
  "flake8 .",
  "black --check --diff .",
  "isort --check-only --diff .",
]
fmt = [
  "isort .",
  "black .",
  "check",
]
```

与[make](https://www.gnu.org/software/make/manual/html_node/Errors.html)类似，您可以忽略以 `-`（连字符）开头的命令的退出代码。例如，以下配置中定义的脚本 `error` 在第二个命令之后会停止，退出代码为 `3`：

```toml config-example
[tool.hatch.envs.test.scripts]
error = [
  "- exit 1",
  "exit 3",
  "exit 0",
]
```

### 额外脚本

单个脚本也会[继承](#inheritance)父环境的脚本。为确保单个脚本不会覆盖父环境中定义的脚本，您可以使用 `extra-scripts` 选项，它只能添加尚未定义的脚本。

## 命令

所有命令都可以使用任何定义的[脚本](#scripts)。与脚本类似，[上下文格式化](advanced.md#context-formatting)也得到了支持，且以连字符开头的命令的退出代码会被忽略。

### 安装前

您可以在环境[安装](#skip-install)项目之前立即运行命令。

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
pre-install-commands = [
  "...",
]
```

### 安装后

您可以在环境[安装](#skip-install)项目之后立即运行命令。

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
post-install-commands = [
  "...",
]
```

## Python 版本

`python` 选项指定要使用的 Python 版本，或 Python 解释器的绝对路径：

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
python = "3.10"
```

所有[环境类型](#type)应遵循此选项。

## 支持的平台

`platforms` 选项表示该环境兼容的操作系统：

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
platforms = ["linux", "windows", "macos"]
```

支持的平台如下：

- `linux`
- `windows`
- `macos`

如果未指定，则假定环境与所有平台兼容。

## 描述

`description` 选项纯粹是信息性的，它将在 [`env show`](../../cli/reference.md#hatch-env-show) 命令的输出中显示：

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
description = """
Lorem ipsum ...
"""
```

## 类型

环境的 `type` 决定了使用哪个[环境插件](../../plugins/environment/reference.md)来管理。唯一的内置环境类型是 [`virtual`](../../plugins/environment/virtual.md)，它使用虚拟 Python 环境。