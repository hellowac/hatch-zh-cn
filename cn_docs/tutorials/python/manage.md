# 管理 Python 发行版

-----

[`python`](../../cli/reference.md#hatch-python) 命令组提供了一系列用于管理 Python 发行版的命令，这些发行版可以供其他工具使用。

!!! note
    在使用环境的情况下，通常无需手动管理发行版，因为 Hatch 会在找不到所需版本时[自动](../../plugins/environment/virtual.md#python-resolution)下载并内部管理 Python 发行版。

## 安装位置

有两种方式可以控制 Python 发行版的安装位置。无论哪种方式，都会将每个安装的发行版置于配置位置的一个以发行版命名的子目录中：

1. 全局配置的 [默认安装目录](../../config/hatch.md#python-installations)；
2. 所有 [`python`](../../cli/reference.md#hatch-python) 子命令的 `-d`/`--dir` 选项，它优先于默认目录。

## 安装

要安装一个 Python 发行版，请使用 [`python install`](../../cli/reference.md#hatch-python-install) 命令。例如：

```
hatch python install 3.12
```

此命令将执行以下操作：

1. 下载 `3.12` 版本的 Python；
2. 将其解压到配置的 [默认安装目录](../../config/hatch.md#python-installations) 下名为 `3.12` 的子目录中；
3. 将该安装路径添加到用户的 PATH 环境变量中。

现在你可以直接或通过其他工具使用该版本的 `python` 可执行文件。

!!! note
    要让 PATH 的变更在当前 shell 中生效，需要重启 shell。

### 安装多个版本

你可以一次性安装多个 Python 发行版，只需提供多个版本名称。例如：

```
hatch python install 3.12 3.11 pypy3.10
```

若希望安装所有与你的系统兼容的可用 Python 发行版，可以将发行版名称设置为 `all`：

```
hatch python install all
```

!!! tip
    [更新](#updates) 和 [移除](#removal) 命令也支持该用法。

### 私有安装

默认情况下，安装的 Python 发行版会添加至用户 PATH 中。若要禁用此行为，可使用 `--private` 标志：

```
hatch python install 3.12 --private
```

该方式配合 [目录选项](#location) 可用于创建私有的、隔离的安装环境。

## 查看已安装发行版

使用 [`python show`](../../cli/reference.md#hatch-python-show) 命令可以查看所有可用和已安装的 Python 发行版。例如，若你已安装 `3.12`，可能会看到如下输出：

```
$ hatch python show
    已安装
┏━━━━━━┳━━━━━━━━━┓
┃ 名称 ┃ 版本    ┃
┡━━━━━━╇━━━━━━━━━┩
│ 3.12 │ 3.12.7  │
└──────┴─────────┘
    可用版本
┏━━━━━━━━━━┳━━━━━━━━━┓
┃ 名称     ┃ 版本    ┃
┡━━━━━━━━━━╇━━━━━━━━━┩
│ 3.7      │ 3.7.9   │
├──────────┼─────────┤
│ 3.8      │ 3.8.20  │
├──────────┼─────────┤
│ 3.9      │ 3.9.20  │
├──────────┼─────────┤
│ 3.10     │ 3.10.15 │
├──────────┼─────────┤
│ 3.11     │ 3.11.10 │
├──────────┼─────────┤
│ 3.13     │ 3.13.0  │
├──────────┼─────────┤
│ pypy2.7  │ 7.3.15  │
├──────────┼─────────┤
│ pypy3.9  │ 7.3.15  │
├──────────┼─────────┤
│ pypy3.10 │ 7.3.15  │
└──────────┴─────────┘
```

## 查找发行版路径

要查找已安装发行版的 Python 可执行文件路径，请使用 [`python find`](../../cli/reference.md#hatch-python-find) 命令。例如：

```
$ hatch python find 3.12
/home/.local/share/hatch/pythons/3.12/python/bin/python3
```

若希望输出其父目录路径，可使用 `-p`/`--parent` 选项：

```
$ hatch python find 3.12 --parent
/home/.local/share/hatch/pythons/3.12/python/bin
```

这对于某些不需要直接使用可执行文件、只需其所在目录的工具来说非常实用。

## 更新发行版

要更新已安装的 Python 发行版，请使用 [`python update`](../../cli/reference.md#hatch-python-update) 命令。例如：

```
hatch python update 3.12 3.11 pypy3.10
```

若指定发行版已是最新版本，会显示警告信息：

```
$ hatch python update 3.12
The latest version is already installed: 3.12.7
```

## 移除发行版

要移除已安装的 Python 发行版，请使用 [`python remove`](../../cli/reference.md#hatch-python-remove) 命令。例如：

```
hatch python remove 3.12 3.11 pypy3.10
```
