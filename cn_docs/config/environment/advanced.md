## 高级环境配置

-----

### 上下文格式化

所有环境都支持以下额外的 [上下文格式化](../context.md) 字段：

| 字段      | 描述                                                                 |
|-----------|----------------------------------------------------------------------|
| `env_name` | 环境的名称                                                            |
| `env_type` | 环境的 [类型](overview.md#type)                                        |
| `matrix`   | 其修饰符选择该矩阵变量的值。如果环境不是矩阵的一部分或未由该变量生成，则必须指定默认值作为附加修饰符，例如 `{matrix:version:v1.0.0}`。 |
| `verbosity` | Hatch 的整数详细级别值。支持 `flag` 修饰符，表示 CLI 标志，例如 `-2` 转换为 `-qq`，`1` 转换为 `-v`，`0` 转换为空字符串。也支持额外的标志整数修饰符以调整详细级别。例如，若要默认将命令设为安静模式，可以在命令中使用 `{verbosity:flag:-1}`。 |
| `args`     | 对于 [执行的命令](../../environment.md#command-execution)，任何额外的命令行参数，如果未提供则可以选择默认修饰符 |

### 矩阵

环境可以使用 `matrix` 选项定义一系列矩阵：

```toml config-example
[tool.hatch.envs.test]
dependencies = [
  "pytest"
]

[[tool.hatch.envs.test.matrix]]
python = ["3.10", "3.11"]
version = ["42", "3.14"]

[[tool.hatch.envs.test.matrix]]
python = ["3.11", "3.12"]
version = ["9000"]
feature = ["foo", "bar"]
```

这将生成每个变量组合的环境。

#### 命名

生成的环境的名称将是每个组合变量的值，以短横线分隔，整体前缀为 `<ENV_NAME>`。例如，以下配置：

```toml config-example
[[tool.hatch.envs.test.matrix]]
version = ["42"]
feature = ["foo", "bar"]
```

将会生成以下独特的环境：

```
test.42-foo
test.42-bar
```

**Python 变量**：
如果指定了变量 `py` 或 `python`，它们将在生成结果中排在最前面，并且如果值不是 Python 版本，将以 `py` 为前缀。例如，以下配置：

```toml config-example
[[tool.hatch.envs.test.matrix]]
version = ["42"]
python = ["3.9", "pypy3"]
```

将生成以下环境：

```
test.py3.9-42
test.pypy3-42
```

!!! 注意
    该变量的值设置了 [Python 版本](overview.md#python-version)。

#### 名称格式化

你可以设置 `matrix-name-format` 选项来修改每个变量部分的格式，使用 `{variable}` 和 `{value}` 占位符。例如，以下配置：

```toml config-example
[tool.hatch.envs.test]
matrix-name-format = "{variable}_{value}"

[[tool.hatch.envs.test.matrix]]
version = ["42"]
feature = ["foo", "bar"]
```

将生成以下环境：

```
test.version_42-feature_foo
test.version_42-feature_bar
```

默认情况下，此选项设置为 `{value}`。

#### 默认环境

如果 `default` 环境定义了矩阵，那么生成的名称将不再以环境名称为前缀。这对于只需要一个矩阵系列而不需要任何独立环境的项目来说非常有用。

### 选择

你可以选择根环境来同时目标所有生成的环境。例如，如果你有以下配置：

```toml config-example
[tool.hatch.envs.test]
dependencies = [
  "coverage[toml]",
  "pytest",
  "pytest-cov",
]

[tool.hatch.envs.test.scripts]
cov = 'pytest --cov-report=term-missing --cov-config=pyproject.toml --cov=pkg --cov=tests'

[[tool.hatch.envs.test.matrix]]
python = ["3.11", "3.12"]
version = ["42", "3.14"]
```

你可以用以下命令依次在所有 4 个环境中运行测试：

```
hatch run test:cov
```

### 选项覆盖

你可以根据不同源的条件修改选项，如 [矩阵变量](#matrix-variable-overrides)，使用 `overrides` 表格，并通过 [点语法](https://toml.io/en/v1.0.0#table)为每个声明设置。

```toml config-example
[tool.hatch.envs.<ENV_NAME>.overrides]
<SOURCE>.<CONDITION>.<OPTION> = <VALUE>
```

选项的 [类型](#types) 决定了值的类型。

#### 平台覆盖

可以基于当前平台修改选项，使用 `platform` 源。

```toml config-example
[tool.hatch.envs.test.overrides]
platform.windows.scripts = [
  'run=pytest -m "not io_uring"',
]
```

支持以下平台：

- `linux`
- `windows`
- `macos`

#### 环境变量覆盖

环境变量可以使用 `env` 源修改选项。

```toml config-example
[tool.hatch.envs.test.overrides]
env.GITHUB_ACTIONS.dev-mode = { value = false, if = ["true"] }
```

#### 矩阵变量覆盖

使用的 [矩阵](#matrix) 变量可用于修改其中的选项，使用 `matrix` 源。

```toml config-example
[tool.hatch.envs.test.overrides]
matrix.version.env-vars = "PRODUCT_VERSION"
matrix.auth.features = [
  { value = "oauth", if = ["oauth2"] },
  { value = "kerberos", if = ["kerberos"] },
]

[[tool.hatch.envs.test.matrix]]
python = ["3.11", "3.12"]
version = ["legacy", "latest"]
auth = ["oauth2", "kerberos", "noauth"]
```

#### 名称覆盖

当定义了 [矩阵](#matrix) 时，`name` 源可用于对生成的名称进行正则表达式匹配，不包括非 [默认](#default-environment) 环境的前缀。

```toml config-example
[tool.hatch.envs.test.overrides]
name."^0".env-vars = "TESTING_UNSTABLE=true"

[[tool.hatch.envs.test.matrix]]
version = ["0.1.0", "0.2.0", "1.0.0"]
```

### 类型

- 文字类型，例如字符串（用于 [Python 版本](overview.md#python-version)）或布尔值（用于 [跳过安装](overview.md#skip-install)）可以使用值本身、内联表或数组来设置。例如：

    ```toml config-example
    [tool.hatch.envs.test.overrides]
    matrix.foo.python = "3.10"
    matrix.bar.skip-install = { value = true, if = ["..."] }
    env.CI.dev-mode = [
      { value = false, if = ["..."] },
      true,
    ]
    ```

    对于数组，将使用第一个允许的值。

- 数组类型，例如 [依赖项](overview.md#dependencies) 或 [命令](overview.md#commands)，可以使用字符串数组或内联表进行追加。例如：

    ```toml config-example
    [tool.hatch.envs.test.overrides]
    matrix.foo.dependencies = [
      "httpx",
      { value = "cryptography" },
    ]
    ```

- 映射类型，例如 [环境变量](overview.md#environment-variables) 或 [脚本](overview.md#scripts)，可以使用字符串、字符串数组或内联表设置键。例如：

    ```toml config-example
    [tool.hatch.envs.test.overrides]
    matrix.foo.env-vars = "KEY=VALUE"
    matrix.bar.env-vars = [
      "KEY1=VALUE1",
      { key = "KEY2", value = "VALUE2" },
    ]
    ```

    如果值缺失（字符串中没有 `=`，内联表中没有 `value` 键），则该值将设置为源条件的值。

### 覆盖

你可以通过在名称前加上 `set-` 来完全覆盖映射类型或数组类型中的选项：

```toml config-example
[tool.hatch.envs.test.overrides]
matrix.foo.set-platforms = ["macos", "linux"]
```

当完全覆盖选项或映射中的键时，覆盖源的应用顺序如下：

1. [平台](#platform-overrides)
2. [环境变量](#environment-variable-overrides)
3. [矩阵变量](#matrix-variable-overrides)
4. [名称](#name-overrides)

### 条件

你可以为任何内联表指定额外的键，以确定是否应用该条目。以下修饰符可以与其他修饰符组合，任何负面评估会立即导致跳过该条目。

#### 允许的值

`if` 键表示该条件的允许值。如果条件的值不在列表中，则该条目不会被应用：

```toml config-example
[tool.hatch.envs.test.overrides]
matrix.version.python = { value = "pypy", if = ["3.14"] }
matrix.version.env-vars = [
  { key = "KEY1", value = "VALUE1", if = ["42"] },
  { key = "KEY2", value = "VALUE2", if = ["3.14"] },
]

[[tool.hatch.envs.test.matrix]]
version = ["42", "3.14"]
```

#### 指定平台

`platform` 键代表所需的平台。如果当前平台未列出，则该条目将不适用：

```toml config-example
[tool.hatch.envs.test.overrides]
env.EXPERIMENTAL.python = { value = "pypy", if = ["1"], platform = ["macos"] }
matrix.version.env-vars = [
  { key = "KEY1", value = "VALUE1", if = ["42"], platform = ["linux"] },
  { key = "KEY2", value = "VALUE2", if = ["3.14"] },
]

[[tool.hatch.envs.test.matrix]]
version = ["42", "3.14"]
```

#### 必需的环境变量

`env` 键表示所需的环境变量。如果列出的任何环境变量未设置或定义的值不匹配，则不会应用该条目：

```toml config-example
[tool.hatch.envs.test.overrides]
platform.windows.python = { value = "pypy", env = ["EXPERIMENTAL"] }
matrix.version.env-vars = [
  { key = "KEY1", value = "VALUE1", if = ["42"], env = ["FOO", "BAR=BAZ"] },
  { key = "KEY2", value = "VALUE2", if = ["3.14"] },
]

[[tool.hatch.envs.test.matrix]]
version = ["42", "3.14"]
```
