# 虚拟环境（Virtual environment）

-----

此插件使用由 [virtualenv](https://github.com/pypa/virtualenv) 或 [UV](https://github.com/astral-sh/uv) 支持的虚拟环境。

## 配置（Configuration）

环境插件名称为 `virtual`。

```toml config-example
[tool.hatch.envs.<ENV_NAME>]
type = "virtual"
```

## 选项（Options）

| 选项 | 默认值 | 描述 |
| --- | --- | --- |
| `python` | | 要在系统中查找的 Python 版本，并随之创建环境，默认值为 `HATCH_PYTHON` 环境变量，其次是 [常规解析逻辑](#python-resolution)。将 `HATCH_PYTHON` 环境变量设置为 `self` 将强制使用 Hatch 正在运行的 Python 可执行文件。更多信息，请参见 [文档](https://virtualenv.pypa.io/en/latest/user_guide.html#python-discovery)。 |
| `python-sources` | `['external', 'internal']` | 可以设置为一个包含 `internal` 或 `external` 字符串的数组。External 仅考虑已在 `PATH` 上的 Python 可执行文件。Internal 仅考虑 [内部管理的 Python 发行版](#internal-distributions)。 |
| `path` | | 虚拟环境的显式路径。路径可以是绝对路径，也可以相对于项目根目录。任何 [继承](../../config/environment/overview.md#inheritance) 此选项的环境也将使用该路径。可以使用环境变量 `HATCH_ENV_TYPE_VIRTUAL_PATH`，该变量将优先于此选项。 |
| `system-packages` | `false` | 是否允许虚拟环境访问系统的 `site-packages` 目录 |
| `installer` | `pip` | 当设置为 `uv` 时，虚拟环境的创建和依赖管理将使用 [UV](https://github.com/astral-sh/uv) 而非 virtualenv 和 pip。如果打算自己提供 UV，可以设置 `HATCH_ENV_TYPE_VIRTUAL_UV_PATH` 环境变量，该变量应为 UV 可执行文件的绝对路径。此环境变量会隐式将 `installer` 选项设置为 `uv`（如果未设置）。 |

## 位置（Location）

环境的位置根据以下启发式规则确定：

1. `path` 选项
2. 配置的 `virtual` [环境目录](../../config/hatch.md#environments) 中的一个目录，且该目录位于项目根目录内，或者设置为用户主目录下的 `.virtualenvs` 目录
3. 否则，环境将存储在配置的 `virtual` [环境目录](../../config/hatch.md#environments) 中的一个深度嵌套结构中，以支持多个项目

另外，当没有使用 `path` 选项时，`default` 环境的目录名将是规范化的项目名称，以提供更具意义的默认 [Shell](../../cli/reference.md#hatch-shell) 提示符。

## Python 解析（Python resolution）

虚拟环境必然需要一个父级的 Python 安装。以下规则确定如何解析父级 Python。

Python 的选择由 [`python` 选项](#options) 和 `HATCH_PYTHON` 环境变量决定。如果选择是通过环境变量完成的，则解析停止，并且无条件使用该路径。

解析器将基于 [`python-sources` 选项](#options)，并且所有解析出的解释器将确保与项目定义的 [Python 支持](../../config/metadata.md#python-support) 兼容。

如果已选择 Python 版本，则每个解析器将尝试找到满足该版本的解释器。

如果未选择版本，则每个解析器将尝试找到与 Hatch 当前运行的 Python 版本匹配的版本。如果找不到，则每个解析器将尝试找到最高兼容的版本。

!!! note
    一些外部 Python 路径被认为是不稳定的，在解析时会被忽略。例如，如果 Hatch 是通过 Homebrew 安装的，则会忽略 `sys.executable`，因为该解释器可能随时更改或被删除。

!!! note
    当使用 [内部管理的发行版](#internal-distributions) 且可用更新时，解析器会在创建环境之前自动下载最新的发行版。

## 内部发行版（Internal distributions）

以下选项用于内部 Python 解析。

!!! tip
    您可以通过设置 `HATCH_PYTHON_SOURCE_<NAME>` 环境变量来自定义发行版的来源，其中 `<NAME>` 是发行版名称的大写形式，句点替换为下划线，例如 `HATCH_PYTHON_SOURCE_PYPY3_10`。

### CPython

| 名称 |
| --- |
| `3.7` |
| `3.8` |
| `3.9` |
| `3.10` |
| `3.11` |
| `3.12` |
| `3.13` |

发行版的来源是 [python-build-standalone](https://github.com/indygreg/python-build-standalone) 项目。

一些发行版具有可以通过环境变量配置的 [变体](https://gregoryszorc.com/docs/python-build-standalone/main/running.html)。选项可以组合使用。

| 选项 | 平台 | 允许的值 |
| --- | --- | --- |
| `HATCH_PYTHON_VARIANT_CPU` | <ul><li>Linux</li></ul> | <ul><li><code>v1</code></li><li><code>v2</code></li><li><code>v3</code>（默认）</li><li><code>v4</code></li></ul> |
| `HATCH_PYTHON_VARIANT_GIL` | <ul><li>Linux</li><li>Windows</li><li>macOS</li></ul> | <ul><li><code>freethreaded</code></li></ul> |

### PyPy

| 名称 |
| --- |
| `pypy2.7` |
| `pypy3.9` |
| `pypy3.10` |

发行版的来源是 [PyPy](https://www.pypy.org) 项目。

## 故障排除（Troubleshooting）

### macOS SIP

如果需要设置类似 `DYLD_` 或 `LD_` 的链接器环境变量，任何通过 [系统完整性保护](https://en.wikipedia.org/wiki/System_Integrity_Protection) 安全保护的可执行文件，在运行 [命令](../../environment.md#command-execution) 时将无法看到这些环境变量的修改，因为 macOS 会将其删除。

Hatch 会将此类命令视为 Shell 命令，并故意忽略指向受保护 Shell 的路径。这个解决方法适用于大多数用例，但有两种情况可能无法解决：

1. 找不到未受保护的 `sh`、`bash`、`zsh` 或 `fish` 可执行文件。
2. 目标可执行文件是项目的 [脚本](../../config/metadata.md#cli)，并且 [环境位置](#location) 包含空格或超过 124[^1] 个字符。在这种情况下，`pip` 和其他安装程序将创建指向 `/bin/sh` 的入口点（这是受保护的），以避免 shebang 限制。除非更改位置，否则可以通过例如 `python -m pytest` 来调用脚本（如果项目支持通过传递 `__main__.py` 进行调用）。

[^1]: shebang 长度限制通常为 127，但执行路径周围有 3 个字符：`#!<EXE_PATH>\n`
