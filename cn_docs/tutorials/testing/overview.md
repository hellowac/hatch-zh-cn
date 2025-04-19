# 测试项目

-----

[`test`](../../cli/reference.md#hatch-test) 命令（[默认情况下](../../config/internal/testing.md#customize-environment)）使用 [pytest](https://github.com/pytest-dev/pytest)，结合特定插件和 [coverage.py](https://github.com/nedbat/coveragepy)。更多信息请参见 [测试配置](../../config/internal/testing.md)。

大多数项目都可以通过这种方式完成全部测试，无需自定义 [环境](../../config/environment/overview.md)。

## 传递参数

当你不带参数运行 `test` 命令时，`tests` 会作为 [默认参数](../../config/internal/testing.md#default-arguments) 传递给 `pytest`（假设你有一个 `tests` 目录）。例如，以下命令：

```
hatch test
```

大致等同于：

```
pytest tests
```

你可以通过在 `test` 命令后追加参数来传递给 `pytest`。例如，以下命令：

```
hatch test -vv tests/test_foo.py::test_bar
```

大致等同于：

```
pytest -vv tests/test_foo.py::test_bar
```

当 `test` 命令的内建选项与 `pytest` 的选项发生冲突（如 `--help`）时，可以使用 `--` 分隔符强制将参数视为位置参数。例如：

```
hatch test -r -- -r fE -- tests
```

大致等同于：

```
pytest -r fE -- tests
```

!!! note
    要确保 `pytest` 能够收到指示要运行哪些测试或从哪里查找测试的参数。其默认行为是 `.`，意味着它会从当前目录递归查找测试文件。这不仅可能很慢，还可能导致意外行为。

## 环境选择

### 单一环境

如果未指定任何环境选项，`test` 命令只会在第一个已定义且兼容的环境中运行测试。查找顺序会优先选择定义了与 Hatch 运行的解释器版本一致的 [Python 版本](../../config/environment/overview.md#python-version) 的环境。

例如，若你覆盖了 [默认 matrix](../../config/internal/testing.md#matrix) 如下：

```toml
[[tool.hatch.envs.hatch-test.matrix]]
python = ["3.12", "3.11"]

[[tool.hatch.envs.hatch-test.matrix]]
python = ["3.11"]
feature = ["foo", "bar"]
```

则扩展后的环境为：

```
hatch-test.py3.12
hatch-test.py3.11
hatch-test.py3.11-foo
hatch-test.py3.11-bar
```

如果你是在 Python 3.11 上安装的 Hatch，则查找顺序为：

```
hatch-test.py3.11
hatch-test.py3.11-foo
hatch-test.py3.11-bar
hatch-test.py3.12
```

!!! note
    如果你使用官方 [安装器](../../install.md#installers) 安装 Hatch，或使用了某个 [独立二进制文件](../../install.md#standalone-binaries)，那么 Hatch 运行的 Python 版本是不可控的。如果你依赖此单一环境解析行为，建议根据 Python 版本 [显式选择环境](#specific-environments)。

### 所有环境

你可以使用 `--all` 标志在所有兼容的环境中运行测试。例如，若你定义了如下 matrix 和 [overrides](../../config/environment/advanced.md#option-overrides)：

```toml
[[tool.hatch.envs.hatch-test.matrix]]
python = ["3.12", "3.11"]
feature = ["foo", "bar"]

[tool.hatch.envs.hatch-test.overrides]
matrix.feature.platforms = [
  { value = "linux", if = ["foo", "bar"] },
  { value = "windows", if = ["foo"] },
  { value = "macos", if = ["bar"] },
]
```

则以下表格表示在哪些环境下将运行测试：

| 环境 | Linux | Windows | macOS |
| --- | --- | --- | --- |
| `hatch-test.py3.12-foo` | ✅ | ✅ | ❌ |
| `hatch-test.py3.12-bar` | ✅ | ❌ | ✅ |
| `hatch-test.py3.11-foo` | ✅ | ✅ | ❌ |
| `hatch-test.py3.11-bar` | ✅ | ❌ | ✅ |

### 指定环境

你可以使用 `--include`/`-i` 和 `--exclude`/`-x` 选项选择环境的子集。这些选项可用于包含或排除某些 matrix 变量，支持指定逗号分隔的值，且可以重复使用。

例如，定义如下 matrix：

```toml
[[tool.hatch.envs.hatch-test.matrix]]
python = ["3.12", "3.11"]
feature = ["foo", "bar", "baz"]
```

若你想在 Python 3.12 且具有 `foo` 或 `bar` 特性的环境中运行测试，可以使用：

```
hatch test -i python=3.12 -i feature=foo,bar
```

或者，也可以通过排除 `baz` 特性达到相同目的：

```
hatch test -i python=3.12 -x feature=baz
```

!!! tip
    由于选择 Python 版本是常见需求，可使用 `--python`/`-py` 作为简写。例如，上述命令可写为：

    ```
    hatch test -py 3.12 -i feature=foo,bar
    hatch test -py 3.12 -x feature=baz
    ```

## 测量代码覆盖率

你可以使用 `--cover` 标志启用 [代码覆盖率](https://github.com/nedbat/coveragepy)。例如，以下命令：

```
hatch test --cover
```

大致等同于：

```
coverage run -m pytest tests
```

在所有 [选定环境](#environment-selection) 运行测试后，覆盖率数据将被合并，并显示报告。`--cover-quiet` 标志可用于隐藏报告，同时隐式启用 `--cover` 标志：

```
hatch test --cover-quiet
```

!!! note
    覆盖率数据文件会生成在项目根目录。请确保使用如下通配符模式将其从版本控制中排除：

    ```
    .coverage*
    ```

## 重试失败测试

你可以使用 `--retries` 选项来 [重试](https://github.com/pytest-dev/pytest-rerunfailures) 失败的测试：

```
hatch test --retries 2
```

如果某个测试每次都失败，且重试次数为 `2`，则该测试总共会运行三次。

你还可以使用 `--retry-delay` 设置每次重试之间的等待秒数：

```
hatch test --retries 2 --retry-delay 1
```

## 并行化测试执行

你可以使用 `--parallel`/`-p` 标志 [并行化](https://github.com/pytest-dev/pytest-xdist) 测试执行：

```
hatch test --parallel
```

这会将单个环境内的测试任务分发给多个工作进程。工作进程的数量取决于系统的逻辑 CPU 数量（而非物理核心数）。

## 随机化测试顺序

你可以使用 `--randomize`/`-r` 标志 [随机化](https://github.com/pytest-dev/pytest-randomly) 测试顺序：

```
hatch test --randomize
```
