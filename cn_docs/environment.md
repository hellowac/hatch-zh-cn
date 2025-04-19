# 环境

-----

[环境](config/environment/overview.md) 的设计目的是为了为测试、构建文档或项目所需的其他操作提供隔离的工作空间。

除非你 [显式指定](#选择环境) 环境，否则 Hatch 将使用 `default`（默认）环境。

!!! tip
    若想了解更完整的流程，请参阅 [基础用法](tutorials/environment/basic-usage.md) 教程。

## 创建环境

可以使用 [`env create`](cli/reference.md#hatch-env-create) 命令创建环境。假设你已经进入了我们在 [项目设置](intro.md#new-project) 阶段创建的项目目录：

```console
$ hatch env create
Creating environment: default
Installing project in development mode
Syncing dependencies
```

!!! tip
    通常不需要手动创建环境，[进入 shell](#进入环境) 或 [运行命令](#执行命令) 时会自动创建。

## 进入环境

可以使用 [`shell`](cli/reference.md#hatch-shell) 命令在某个环境中启动一个 [shell](config/hatch.md#shell)：

```console
$ hatch shell
(hatch-demo) $
```

此时你可以确认项目已经被安装：

```console
(hatch-demo) $ pip show hatch-demo
Name: hatch-demo
Version: 0.0.1
...
```

最后，可以查看该环境所使用的 Python 可执行文件位置（参见 [配置文档](config/hatch.md#environments)）：

```console
(hatch-demo) $ python -c "import sys;print(sys.executable)"
...
```

输入 `exit` 即可离开环境。

## 执行命令

[`run`](cli/reference.md#hatch-run) 命令允许你在某个环境中直接执行命令，就好像你已经进入了该环境。例如，以下命令的输出路径应与前面相同：

```
hatch run python -c "import sys;print(sys.executable)"
```

!!! tip
    请务必了解如何为项目定义 [脚本](config/environment/overview.md#scripts)。

## 依赖项

Hatch 会确保环境始终与你所定义的 [项目依赖项](config/metadata.md#dependencies)（如果 [已安装](config/environment/overview.md#skip-install)，并启用了 [开发模式](config/environment/overview.md#dev-mode)）以及 [环境依赖项](config/environment/overview.md#dependencies) 保持兼容。

若要添加 `cowsay` 作为依赖项，请打开 `pyproject.toml`，将其添加到 [`dependencies`](config/metadata.md#dependencies) 数组中：

```toml tab="pyproject.toml"
[project]
...
dependencies = [
  "cowsay"
]
```

下次你 [进入 shell](#进入环境) 或 [运行命令](#执行命令) 时，该依赖项将被自动安装。例如：

```console
$ hatch run cowsay -t "Hello, world!"
Syncing dependencies
  _____________
| Hello, world! |
  =============
             \
              \
                ^__^
                (oo)\_______
                (__)\       )\/\
                    ||----w |
                    ||     ||
```

!!! note
    每当你更改依赖项，Hatch 会更新环境，届时会短暂显示 `Syncing dependencies` 状态信息。

## 选择环境

你可以使用 `-e`/`--env` [根选项](cli/reference.md#hatch) 或设置 `HATCH_ENV` 环境变量来选择要进入或运行命令的环境。

[`run`](cli/reference.md#hatch-run) 命令也支持更显式的方式，通过在命令前加上 `<环境名>:`。例如，若你的配置如下：

```toml config-example
[tool.hatch.envs.docs]
dependencies = [
  "mkdocs"
]
[tool.hatch.envs.docs.scripts]
build = "mkdocs build --clean --strict"
serve = "mkdocs serve --dev-addr localhost:8000"
```

你可以通过以下命令启动文档服务：

```
hatch run docs:serve
```

!!! tip
    若你已经 [进入了某个环境](#进入环境)，则默认目标环境为当前所处的环境。

## 矩阵

每个环境都可以定义自己的 [矩阵](config/environment/advanced.md#matrix)：

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

使用 [`env show`](cli/reference.md#hatch-env-show) 命令即可查看：

```console
$ hatch env show --ascii
     Standalone
+---------+---------+
| Name    | Type    |
+=========+=========+
| default | virtual |
+---------+---------+
                        Matrices
+------+---------+----------------------+--------------+
| Name | Type    | Envs                 | Dependencies |
+======+=========+======================+==============+
| test | virtual | test.py3.10-42       | pytest       |
|      |         | test.py3.10-3.14     |              |
|      |         | test.py3.11-42       |              |
|      |         | test.py3.11-3.14     |              |
|      |         | test.py3.11-9000-foo |              |
|      |         | test.py3.11-9000-bar |              |
|      |         | test.py3.12-9000-foo |              |
|      |         | test.py3.12-9000-bar |              |
+------+---------+----------------------+--------------+
```

## 删除环境

你可以使用 [`env remove`](cli/reference.md#hatch-env-remove) 命令删除单个环境或某个矩阵下的环境，也可以使用 [`env prune`](cli/reference.md#hatch-env-prune) 命令删除项目下的所有环境。