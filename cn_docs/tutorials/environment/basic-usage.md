# 管理环境

-----

Hatch 的[环境](../../environment.md)是隔离的工作空间，可用于项目任务，如运行测试、构建文档、执行代码格式化工具和代码检查工具等。

## 默认环境

当你开始使用 Hatch 时，可以创建一个名为 `default` 的默认环境。可使用 [`env create`](../../cli/reference.md#hatch-env-create) 命令完成：

```
hatch env create
```

这不仅会为你创建一个 `default` 环境，还会将你的项目以[开发模式](../../config/environment/overview.md#dev-mode)安装到该环境中。

!!! tip
    你无需手动创建环境，[启动一个 shell](#在特定环境中启动-shell) 或 [在某环境中运行命令](#在特定环境中运行命令) 时会自动触发创建。

### 使用默认环境

若未显式指定环境，Hatch 总是使用 `default` 环境来[运行命令](../../environment.md#command-execution)。

例如，以下命令用于获取当前 Python 的版本信息：

```console
$ hatch run python -V
Python 3.12.1
```

### 配置默认环境

你可以通过在 `pyproject.toml` 文件中添加一个名为 `tool.hatch.envs.default` 的表来定制 `default` 环境中安装的工具。下面示例中将 [依赖项](../../config/environment/overview.md#dependencies) `pydantic` 和 `numpy` 添加到了默认环境中：

```toml config-example
[tool.hatch.envs.default]
dependencies = [
  "pydantic",
  "numpy",
]
```

你也可以在该配置中为依赖项声明具体版本：

```toml config-example
[tool.hatch.envs.default]
dependencies = [
  "pydantic>=2.0",
  "numpy",
]
```

## 创建自定义环境

你可以通过在 `pyproject.toml` 文件中添加 `[tool.hatch.envs.<ENV_NAME>]` 小节来创建自定义环境。下面定义了一个名为 `test` 的环境，并添加了 `pytest` 和 `pytest-cov` 作为该环境的依赖项：

```toml config-example
[tool.hatch.envs.test]
dependencies = [
  "pytest",
  "pytest-cov"
]
```

首次调用该测试环境时，Hatch 将会：

1. 创建该环境；
2. 将你的项目以[开发模式](../../config/environment/overview.md#dev-mode)（默认）安装进该环境，并安装其 [依赖项](../../config/metadata.md#dependencies)；
3. 安装该环境的[依赖项](../../config/environment/overview.md#dependencies)。

## 在特定环境中运行命令

Hatch 提供了一个独特的环境特性：你可以在某个特定环境中直接运行命令，而无需像使用 [Conda](https://conda.org) 或 [venv](https://docs.python.org/3/library/venv.html) 那样先激活环境。

例如，如果你定义了一个名为 `test` 的环境，包含前述依赖项，则可使用以下语法运行 `pytest` 命令：

```
hatch run <ENV_NAME>:command
```

要在 `test` 环境中运行 `pytest`，可以执行：

```console
$ hatch run test:pytest
============================== test session starts ===============================
platform darwin -- Python 3.12.1, pytest-7.4.4, pluggy-1.3.0
rootdir: /your/path/to/yourproject
collected 0 items
```

!!! note
    `test:pytest` 中 `test` 是环境名，`pytest` 是要运行的命令。

## 查看当前环境

上文中你已在 `pyproject.toml` 文件中定义并创建了一个新的测试环境。现在可以使用 [`env show`](../../cli/reference.md#hatch-env-show) 命令查看当前已创建的环境及其依赖项：

```
$ hatch env show
             Standalone
┏━━━━━━━━━┳━━━━━━━━━┳━━━━━━━━━━━━━━┓
┃ Name    ┃ Type    ┃ Dependencies ┃
┡━━━━━━━━━╇━━━━━━━━━╇━━━━━━━━━━━━━━┩
│ default │ virtual │              │
├─────────┼─────────┼──────────────┤
│ test    │ virtual │ pytest       │
│         │         │ pytest-cov   │
└─────────┴─────────┴──────────────┘
```

!!! note
    输出内容的列数可能因你的环境配置而有所不同。

## 查找环境位置

要查看当前环境的位置，可使用 [`env find`](../../cli/reference.md#hatch-env-find) 命令：

```
$ hatch env find test
/your/path/Application Support/hatch/env/virtual/yourproject/twO2iQR3/test
```

!!! note
    上述路径为 macOS 上的输出，其他平台不同，且路径可[自定义配置](../../plugins/environment/virtual.md#location)。

## 在特定环境中启动 shell

若你想为已创建的特定环境（如前述 `test` 环境）[启动 shell](../../environment.md#entering-environments)，可使用以下命令：

```
hatch -e test shell
```

环境激活后，就可像在普通 Python 环境中一样运行命令了。

如下所示，当你在 `test` 环境中运行 `pip list`，可以看到：

1. 你的项目是以可编辑模式安装的；
2. 环境中包含了 `pytest` 和 `pytest-cov`，正如在 `pyproject.toml` 文件中所指定的。

```
$ pip list
Package     Version Editable project location
----------- ------- ----------------------------------------------------
coverage    7.4.1
iniconfig   2.0.0
packaging   23.2
pip         23.3.1
pluggy      1.4.0
pytest      8.0.0
pytest-cov  4.1.0
yourproject 0.1.0  /your/path/to/yourproject
```

## Conda 环境

如果你更喜欢将 [Conda](https://conda.org) 与 Hatch 一起使用，可以查看 [hatch-conda 插件](https://github.com/OldGrumpyViking/hatch-conda)。
