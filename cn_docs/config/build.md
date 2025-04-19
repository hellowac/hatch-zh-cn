# 构建配置

-----

[构建目标](#build-targets) 在 `tool.hatch.build.targets` 中定义为各个部分：

```toml config-example
[tool.hatch.build.targets.<TARGET_NAME>]
```

!!! tip
    尽管不推荐，您可以在 `tool.hatch.build` 表中定义全局配置。然后可以通过目标配置覆盖这些键。

## 构建系统

为了与更广泛的 [Python 打包生态系统](../build.md#packaging-ecosystem) 兼容，您必须按如下方式定义 [构建系统](https://peps.python.org/pep-0517/#source-trees)：

```toml tab="pyproject.toml"
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

此处定义的 `hatchling` 版本将用于构建所有目标。

Hatchling 是符合标准的[^1] 构建后端，并且是 Hatch 本身的依赖项。

## 文件选择

### VCS

默认情况下，Hatch 会尊重项目根目录或父目录中找到的第一个 `.gitignore` 或 `.hgignore` 文件。如果要禁用此行为，请将 `ignore-vcs` 设置为 `true`：

```toml config-example
[tool.hatch.build.targets.sdist]
ignore-vcs = true
```

!!! note
    对于 `.hgignore` 文件，仅支持 glob 语法。

### 模式

您可以设置 `include` 和 `exclude` 选项，以精确选择每次构建中将包含的文件，其中 `exclude` 优先于 `include`。每个条目代表一个 [Git 样式的 glob 模式](https://git-scm.com/docs/gitignore#_pattern_format)。

例如，以下配置：

```toml config-example
[tool.hatch.build.targets.sdist]
include = [
  "pkg/*.py",
  "/tests",
]
exclude = [
  "*.json",
  "pkg/_compat.py",
]
```

将排除所有 `.json` 扩展名的文件，并将根目录下的 `tests` 目录中的所有文件以及根目录下 `pkg` 目录下的所有 `.py` 文件（除 `_compat.py` 外）包括在内。

### 工件

如果您想包括那些被 [VCS 忽略](#vcs) 的文件，如可能由 [构建钩子](#build-hooks) 创建的文件，可以使用 `artifacts` 选项。此选项与 `include` 等效。

请注意，工件不受 `exclude` 选项的影响。工件可以通过使用更明确的路径或使用 `!` 否定操作符来排除。使用 `!` 操作符时，否定模式必须放在更通用的模式之后。

```toml config-example
[tool.hatch.build.targets.wheel]
artifacts = [
  "*.so",
  "*.dll",
  "!/foo/*.so",
]
```

### 显式选择

#### 通用

您可以使用 `only-include` 选项来防止从项目根目录开始进行目录遍历，仅选择特定的相对路径到目录或文件。使用此选项时，将忽略任何定义的 [`include` 模式](#patterns)。

```toml config-example
[tool.hatch.build.targets.sdist]
only-include = ["pkg", "tests/unit"]
```

#### 包

`packages` 选项在语义上与 `only-include` 等效（后者优先），不同之处在于，已打包的路径将被压缩为仅包含最终组件。

例如，如果您想要打包存储在 `src` 目录中的 `foo` 包，可以这样做：

```toml config-example
[tool.hatch.build.targets.wheel]
packages = ["src/foo"]
```

### 强制包含

`force-include` 选项允许您从文件系统中的任何位置选择特定的文件或目录，并将它们映射到所需的相对分发路径。

例如，如果在项目根目录旁有一个名为 `artifacts` 的目录，其中包含名为 `lib.so` 的文件，并且在您的主目录中有一个名为 `lib.h` 的文件，您可以通过以下配置将这两个文件都打包到 `pkg` 目录中：

```toml config-example
[tool.hatch.build.targets.wheel.force-include]
"../artifacts" = "pkg"
"~/lib.h" = "pkg/lib.h"
```

!!! note
    - 文件必须精确映射到所需路径，而不是目录。
    - 目录源的内容将递归包含。
    - 若要直接将目录内容映射到根目录，请使用 `/`（正斜杠）。
    - 不存在的源将导致错误。

!!! warning
    使用此选项包含的文件将覆盖通过其他文件选择选项已经包含的任何文件路径。

### 默认文件选择

如果未提供文件选择选项，则默认的包含项由每个 [构建目标](#build-targets) 决定。

### 排除包外的文件

如果您想排除那些不位于 Python 包中的非 [工件](#artifacts) 文件，请将 `only-packages` 设置为 `true`：

```toml config-example
[tool.hatch.build.targets.wheel]
only-packages = true
```

### 重写路径

您可以使用 `sources` 选项重写目录的相对路径。例如，以下配置：

```toml config-example
[tool.hatch.build.targets.wheel.sources]
"src/foo" = "bar"
```

会将文件 `src/foo/file.ext` 作为 `bar/file.ext` 进行分发。

如果您希望完全去掉路径前缀，而不是将其设置为空字符串，可以将 `sources` 定义为数组：

```toml config-example
[tool.hatch.build.targets.wheel]
sources = ["src"]
```

如果您想为路径添加前缀，可以使用空字符串。例如，以下配置：

```toml config-example
[tool.hatch.build.targets.wheel.sources]
"" = "foo"
```

将把文件 `bar/file.ext` 分发为 `foo/bar/file.ext`。

[packages](#packages) 选项本身依赖于 sources。为 `wheel` 目标定义 `#!toml packages = ["src/foo"]` 相当于以下配置：

```toml config-example
[tool.hatch.build.targets.wheel]
only-include = ["src/foo"]
sources = ["src"]
```

### 性能

默认情况下，所有遇到的目录都会被遍历。为了跳过那些被排除的非 [工件](#artifacts) 目录，请将 `skip-excluded-dirs` 设置为 `true`：

```toml config-example
[tool.hatch.build]
skip-excluded-dirs = true
```

!!! warning
    这可能导致未打包预期的文件。例如，如果您希望包含文件 `a/b/c.txt`，但您的 [VCS 忽略](#vcs) 了 `a/b`，则文件 `c.txt` 将无法看到，因为它的父目录将不会被进入。在这种情况下，您可以使用 [`force-include`](#forced-inclusion) 选项。

## 可复现构建

默认情况下，[构建目标](#build-targets) 会以可复现的方式进行构建，前提是它们支持该行为。要禁用此行为，请将 `reproducible` 设置为 `false`：

```toml config-example
[tool.hatch.build]
reproducible = false
```

启用时，所有构建时间戳都将使用 [SOURCE_DATE_EPOCH](https://reproducible-builds.org/specs/source-date-epoch/) 环境变量。如果未设置，则 Hatch 将使用一个 [不变的默认值](../plugins/utilities.md#hatchling.builders.utils.get_reproducible_timestamp)。

## 输出目录

当未向 [`build`](../cli/reference.md#hatch-build) 命令提供输出目录时，默认将使用 `dist` 目录。您可以使用相对路径或绝对路径来更改默认路径：

```toml config-example
[tool.hatch.build]
directory = "<PATH>"
```

## 开发模式

对于 [开发模式](environment/overview.md#dev-mode) 环境安装或 [可编辑安装](https://pip.pypa.io/en/stable/topics/local-project-installs/#editable-installs)，默认情况下，`wheel` 目标将根据 [选定的文件](#file-selection) 确定哪些目录应添加到 Python 的搜索路径中。

如果您想覆盖此检测或指示其他构建目标，也可以使用 `dev-mode-dirs` 选项：

```toml config-example
[tool.hatch.build]
dev-mode-dirs = ["."]
```

如果您不希望将整个目录添加到 Python 的搜索路径中，可以启用更精确的机制，使用互斥的 `dev-mode-exact` 选项：

```toml config-example
[tool.hatch.build]
dev-mode-exact = true
```

!!! warning
    `dev-mode-exact` 机制 [不被支持](https://github.com/microsoft/pylance-release/issues/2114) 由静态分析工具和 IDE，因此诸如自动补全之类的功能可能无法正常工作。

## 构建目标

构建目标可以由任何 [构建插件](../plugins/builder/reference.md) 提供。共有三种内置构建目标：[wheel](../plugins/builder/wheel.md)、[sdist](../plugins/builder/sdist.md) 和 [custom](../plugins/builder/custom.md)。

### 依赖项 ### {: #target-dependencies }

您可以指定在每个构建环境中要安装的额外依赖项，例如用于第三方构建器插件：

```toml config-example
[tool.hatch.build.targets.your-target-name]
dependencies = [
  "your-builder-plugin"
]
```

您还可以通过 `require-runtime-dependencies` 选项声明对项目的 [运行时依赖项](metadata.md#required) 的依赖：

```toml config-example
[tool.hatch.build.targets.your-target-name]
require-runtime-dependencies = true
```

此外，您可以通过 `require-runtime-features` 选项声明对项目特定 [运行时功能](metadata.md#optional) 的依赖：

```toml config-example
[tool.hatch.build.targets.your-target-name]
require-runtime-features = [
  "feature1",
  "feature2",
]
```

### 版本

如果构建目标支持多种构建策略，或者随着时间的推移发生了重大变化，您可以通过 `versions` 选项指定要构建的确切版本：

```toml config-example
[tool.hatch.build.targets.<TARGET_NAME>]
versions = [
  "v1",
  "beta-feature",
]
```

请参阅 [wheel](../plugins/builder/wheel.md#versions) 目标的实际示例。

## 构建钩子

构建钩子定义了将在构建过程的各个阶段执行的代码，并且可以由任何 [构建钩子插件](../plugins/build-hook/reference.md) 提供。内置的构建钩子有一个：[custom](../plugins/build-hook/custom.md)。

构建钩子可以全局应用：

```toml config-example
[tool.hatch.build.hooks.<HOOK_NAME>]
```

也可以应用于特定的构建目标：

```toml config-example
[tool.hatch.build.targets.<TARGET_NAME>.hooks.<HOOK_NAME>]
```

### 依赖项 ### {: #hook-dependencies }

您可以指定在每个构建环境中要安装的额外依赖项，例如用于第三方构建钩子插件：

```toml config-example
[tool.hatch.build.hooks.your-hook-name]
dependencies = [
  "your-build-hook-plugin"
]
```

您还可以通过 `require-runtime-dependencies` 选项声明对项目的 [运行时依赖项](metadata.md#required) 的依赖：

```toml config-example
[tool.hatch.build.hooks.your-hook-name]
require-runtime-dependencies = true
```

此外，您可以通过 `require-runtime-features` 选项声明对项目特定 [运行时功能](metadata.md#optional) 的依赖：

```toml config-example
[tool.hatch.build.hooks.your-hook-name]
require-runtime-features = [
  "feature1",
  "feature2",
]
```

### 执行顺序

对于每个构建目标，构建钩子的执行顺序是按定义顺序执行的，从全局钩子开始。

例如，对于以下配置：

```toml config-example
[tool.hatch.build.targets.foo.hooks.hook2]

[tool.hatch.build.hooks.hook3]
[tool.hatch.build.hooks.hook1]
```

当构建目标 `foo` 时，构建钩子 `hook3` 将首先执行，然后是 `hook1`，最后是 `hook2`。

### 条件执行

如果您希望默认禁用构建钩子并通过 [环境变量](#environment-variables) 控制其使用，可以将 `enable-by-default` 设置为 `false`：

```toml config-example
[tool.hatch.build.hooks.<HOOK_NAME>]
enable-by-default = false
```

## 环境变量

| 变量 | 默认值 | 描述 |
| --- | --- | --- |
| `HATCH_BUILD_CLEAN` | `false` | 是否在构建之前先删除现有的工件 |
| `HATCH_BUILD_CLEAN_HOOKS_AFTER` | `false` | 是否在每次构建后删除构建钩子的工件 |
| `HATCH_BUILD_HOOKS_ONLY` | `false` | 是否仅执行构建钩子 |
| `HATCH_BUILD_NO_HOOKS` | `false` | 是否禁用所有构建钩子；此选项优先于其他选项 |
| `HATCH_BUILD_HOOKS_ENABLE` | `false` | 是否启用所有构建钩子 |
| `HATCH_BUILD_HOOK_ENABLE_<HOOK_NAME>` | `false` | 是否启用名为 `<HOOK_NAME>` 的构建钩子 |
| `HATCH_BUILD_LOCATION` | `dist` | 用于构建目标的位置；仅在 [`build`](../cli/reference.md#hatch-build) 命令中使用 |

[^1]: 支持 [PEP 517][] 和 [PEP 660][] 保证与其他构建工具的互操作性。