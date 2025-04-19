# Hatch 配置

-----

Hatch 本身的配置保存在一个 `config.toml` 文件中，默认位于以下平台特定目录之一：

| 平台 | 路径 |
| --- | --- |
| macOS | `~/Library/Application Support/hatch` |
| Windows | `%USERPROFILE%\AppData\Local\hatch` |
| Unix | `$XDG_CONFIG_HOME/hatch`（`XDG_CONFIG_HOME` 环境变量默认值为 `~/.config`）|

你可以使用 `--config` [根选项](../cli/reference.md#hatch) 或设置 `HATCH_CONFIG` 环境变量来自定义该文件的路径。

该配置文件可通过 [`config`](../cli/reference.md#hatch-config) 命令组进行管理。

## 模式（Mode）

`mode` 键控制 Hatch 如何选择当前操作的项目。

### Local（本地模式）

```toml tab="config.toml"
mode = "local"
```

默认情况下，Hatch 会在当前工作目录及其父目录中查找 `pyproject.toml` 文件。第一个找到的文件所在目录将被视为项目根目录。

### Project（指定项目模式）

```toml tab="config.toml"
mode = "project"
project = "proj1"

[projects]
proj1 = "/path/to/project1"
proj2 = {"location": "/path/to/project2"}

[dirs]
project = ["/path/to/monorepo1", "/path/to/monorepo2"]
```

在该模式下，Hatch 仅会操作被选中的 `project` 项。项目的定位方式如下：

1. 若在 `projects` 表中定义了该项目名，则其值必须为字符串或包含 `location` 键的内联表，对应项目的完整路径；
2. 若该项目名与 `dirs.project` 中任何一个目录下的子目录匹配，则该子目录即为项目根路径。

若无法定位该项目，则会抛出错误。

你可以使用 [`config set`](../cli/reference.md#hatch-config-set) 命令切换当前操作的项目：

```console
$ hatch config set project proj2
New setting:
project = "proj2"
```

每条命令也可通过 `-p` / `--project`（环境变量 `HATCH_PROJECT`）[根选项](../cli/reference.md#hatch) 指定项目。

### Aware（自动识别模式）

```toml tab="config.toml"
mode = "aware"
```

该模式实质上是先尝试 `local` 模式，失败时回退到 `project` 模式。

## Shell

你可以使用 `shell` 键控制用于 [进入环境](../environment.md#entering-environments) 的 shell。

若设置为字符串，必须是一个 [支持的 shell](#supported) 名称，且该 shell 可在 `PATH` 中找到：

```toml tab="config.toml"
shell = "fish"
```

若你的 shell 可执行文件名与支持的名称不同，你可将 `shell` 设置为包含 `name` 和 `path` 键的表：

```toml tab="config.toml"
[shell]
name = "bash"
path = "/bin/ash"
```

你还可以通过 `args` 键自定义启动 shell 时使用的参数。对于大多数受支持的 shell，其默认值为 `["-i"]`：

```toml tab="config.toml"
[shell]
name = "bash"
args = ["--login"]
```

### 支持的 shell

| Shell | 名称 | 参数 | macOS | Windows | Unix |
| --- | --- | --- | --- | --- | --- |
| [Almquist shell](https://en.wikipedia.org/wiki/Almquist_shell) | `ash` | `["-i"]` | ✅ | | ✅ |
| [Bash](https://www.gnu.org/software/bash/) | `bash` | `["-i"]` | ✅ | ✅ | ✅ |
| [命令提示符](https://en.wikipedia.org/wiki/Cmd.exe) | `cmd` | | | ✅ | |
| [C shell](https://en.wikipedia.org/wiki/C_shell) | `csh` | `["-i"]` | ✅ | | ✅ |
| [fish](https://github.com/fish-shell/fish-shell) | `fish` | `["-i"]` | ✅ | | ✅ |
| [Nushell](https://github.com/nushell/nushell) | `nu` | `[]` | ✅ | ✅ | ✅ |
| [PowerShell](https://github.com/PowerShell/PowerShell) | `pwsh`, `powershell` | | ✅ | ✅ | ✅ |
| [tcsh](https://en.wikipedia.org/wiki/Tcsh) | `tcsh` | `["-i"]` | ✅ | | ✅ |
| [xonsh](https://github.com/xonsh/xonsh) | `xonsh` | `["-i"]` | ✅ | ✅ | ✅ |
| [Z shell](https://en.wikipedia.org/wiki/Z_shell) | `zsh` | `["-i"]` | ✅ | | ✅ |

### 默认行为

Hatch 会尝试根据父进程推断当前 shell。若无法确定：

- Windows 系统将依次使用 `SHELL` 环境变量、`COMSPEC` 环境变量，最后默认使用 `cmd`；
- 其他系统将使用 `SHELL` 环境变量，默认值为 `bash`。

## 目录（Directories）

### 数据目录

```toml tab="config.toml"
[dirs]
data = "..."
```

用于持久化数据的目录。默认路径如下：

| 平台 | 路径 |
| --- | --- |
| macOS | `~/Library/Application Support/hatch` |
| Windows | `%USERPROFILE%\AppData\Local\hatch` |
| Unix | `$XDG_DATA_HOME/hatch`（默认 `~/.local/share`）|

可使用 `--data-dir` [根选项](../cli/reference.md#hatch) 或设置 `HATCH_DATA_DIR` 环境变量自定义路径。

### 缓存目录

```toml tab="config.toml"
[dirs]
cache = "..."
```

用于缓存数据的目录。默认路径如下：

| 平台 | 路径 |
| --- | --- |
| macOS | `~/Library/Caches/hatch` |
| Windows | `%USERPROFILE%\AppData\Local\hatch\Cache` |
| Unix | `$XDG_CACHE_HOME/hatch`（默认 `~/.cache`）|

可使用 `--cache-dir` 或设置 `HATCH_CACHE_DIR` 自定义该路径。

### 环境目录

```toml tab="config.toml"
[dirs.env]
<ENV_TYPE> = "..."
```

每种 [环境类型](environment/overview.md#type) 可指定不同的存储路径。

例如，若希望将 [虚拟环境](../plugins/environment/virtual.md) 保存在用户主目录下的 `.virtualenvs` 目录中：

```toml tab="config.toml"
[dirs.env]
virtual = "~/.virtualenvs"
```

支持环境变量展开。

若路径非绝对路径，则以项目根目录为基准。例如，若想在每个项目中使用 `.hatch` 目录：

```toml tab="config.toml"
[dirs.env]
virtual = ".hatch"
```

未显式设置的环境类型将默认存储在 `<DATA_DIR>/env/<ENV_TYPE>` 路径下。

### Python 安装目录

```toml tab="config.toml"
[dirs]
python = "..."
```

指定特定版本 Python 的安装位置。

特殊值如下：

| 值 | 路径 |
| --- | --- |
| `isolated`（默认） | `<DATA_DIR>/pythons` |

## 终端输出（Terminal）

可通过 `terminal.styles` 表自定义所有输出样式，插件也会使用相同设置：

```toml tab="config.toml"
[terminal.styles]
error = "..."
...
```

跨平台终端功能由 [Rich](https://github.com/Textualize/rich) 提供。

### 输出级别

输出级别如下所示（[verbosity](../cli/about.md) 指示显示该级别输出的最小冗余级别）：

| 级别 | 默认样式 | 冗余等级 | 说明 |
| --- | --- | ---: | --- |
| `debug` | `bold` | 1 - 3 | 通常用户无需关注的调试信息 |
| `error` | `bold red` | -2 | 表示不可恢复的错误 |
| `info` | `bold` | 0 | 基本信息 |
| `success` | `bold cyan` | 0 | 成功提示 |
| `waiting` | `bold magenta` | 0 | 表示耗时操作前的提示信息 |
| `warning` | `bold yellow` | -1 | 表示重要警告信息 |

详见 [样式文档](https://rich.readthedocs.io/en/latest/style.html) 与 [颜色参考](https://rich.readthedocs.io/en/latest/appendix/colors.html)。

### 动画（Spinner）

可通过 `spinner` 选项设置等待动画的 [样式序列](https://github.com/Textualize/rich/blob/master/rich/_spinners.py)：

```toml tab="config.toml"
[terminal.styles]
spinner = "..."
```
