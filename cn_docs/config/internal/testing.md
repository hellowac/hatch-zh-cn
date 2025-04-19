# 测试配置

-----

请参阅[测试概览教程](../../tutorials/testing/overview.md)，获取更全面的操作指南。

## 设置项（Settings）

如果某个选项在 [`test`](../../cli/reference.md#hatch-test) 命令中有对应的命令行参数，则该参数始终优先生效。

### 默认参数

你可以通过设置 `default-args` 选项为字符串数组，定义 [`test`](../../cli/reference.md#hatch-test) 命令的默认参数。以下是默认配置：

```toml config-example
[tool.hatch.envs.hatch-test]
default-args = ["tests"]
```

### 额外参数

你可以通过设置 `extra-args` 选项为字符串数组，为测试 [脚本](#scripts) 定义额外的内部参数。例如，如果你希望提高 `pytest` 的输出详细程度，可以如下设置：

```toml config-example
[tool.hatch.envs.hatch-test]
extra-args = ["-vv"]
```

### 测试顺序随机化

你可以启用 `randomize` 选项来[随机化](https://github.com/pytest-dev/pytest-randomly)测试顺序，对应的命令行参数为 `--randomize`/`-r`：

```toml config-example
[tool.hatch.envs.hatch-test]
randomize = true
```

### 测试并行执行

你可以启用 `parallel` 选项来[并行化](https://github.com/pytest-dev/pytest-xdist)测试执行，对应的命令行为 `--parallel`/`-p`：

```toml config-example
[tool.hatch.envs.hatch-test]
parallel = true
```

### 重试失败的测试

你可以设置 `retries` 选项来[重试](https://github.com/pytest-dev/pytest-rerunfailures)失败的测试，对应命令行为 `--retries`：

```toml config-example
[tool.hatch.envs.hatch-test]
retries = 2
```

你还可以通过设置 `retry-delay` 选项来指定重试间隔的秒数，对应命令行为 `--retry-delay`：

```toml config-example
[tool.hatch.envs.hatch-test]
retry-delay = 1
```

## 自定义环境

你可以完全改变 [`test`](../../cli/reference.md#hatch-test) 命令所使用环境的行为。

### 依赖项

你可以定义测试所需的[额外依赖项](../environment/overview.md#dependencies)：

```toml config-example
[tool.hatch.envs.hatch-test]
extra-dependencies = [
  "pyfakefs",
  "pytest-asyncio",
  "pytest-benchmark",
  "pytest-memray",
  "pytest-playwright",
  "pytest-print",
]
```

以下是默认配置：

```toml config-example
<HATCH_TEST_ENV_DEPENDENCIES>
```

### 矩阵（Matrix）

你可以重写默认的一组[矩阵配置](../environment/advanced.md#matrix)：

```toml config-example
<HATCH_TEST_ENV_MATRIX>
```

### 脚本

如果你希望修改默认执行的命令，可以重写 [scripts](../environment/overview.md#scripts)。以下默认脚本必须重新定义：

```toml config-example
<HATCH_TEST_ENV_SCRIPTS>
```

`run` 脚本是默认行为；当需要统计代码覆盖率时，将改用 `run-cov` 脚本。所有测试完成后会执行 `cov-combine` 脚本，如果未使用 `--cover-quiet` 参数，还会执行 `cov-report` 脚本。

!!! note
    `HATCH_TEST_ARGS` 环境变量用于在不影响用户参数的情况下，将 [`test`](../../cli/reference.md#hatch-test) 命令的标志位转换为内部参数。这也是传递[额外参数](#extra-arguments)的方式。

### 安装器（Installer）

默认情况下，[UV 是启用的](../../how-to/environment/select-installer.md)。你可以如下禁用该行为：

```toml config-example
[tool.hatch.envs.hatch-test]
installer = "pip"
```
