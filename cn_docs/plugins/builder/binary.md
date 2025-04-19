# 二进制构建器（Binary builder）

-----

该构建器使用 [PyApp](https://github.com/ofek/pyapp) 来构建一个能够在运行时自我引导（bootstrap）的应用程序。

!!! note
    这需要安装 [Rust](https://www.rust-lang.org)。

## 配置

该构建器插件的名称为 `binary`。

```toml config-example
[tool.hatch.build.targets.binary]
```

## 选项（Options）

| 选项 | 默认值 | 描述 |
| --- | --- | --- |
| `scripts` | 所有已定义项 | 要限制构建范围的 [脚本](../../config/metadata.md#cli) 名称数组 |
| `python-version` | 最新兼容的 Python 次版本 | 要使用的 [Python 版本 ID](https://ofek.dev/pyapp/latest/config/#known) |
| `pyapp-version` | | 要使用的 PyApp 版本 |

## 构建行为（Build behavior）

如果定义了任何 [脚本](../../config/metadata.md#cli)，则每个脚本都会被构建（受 `scripts` 选项限制）。否则，将根据项目名称构建一个可执行文件，前提是存在一个同名模块并包含 `__main__.py` 文件。

所有可执行文件都会被构建到 [输出目录](../../config/build.md#output-directory) 下的 `app` 子目录中。

如果设置了 `CARGO` 环境变量，则构建过程将使用其指定的路径作为可执行程序。

如果设置了 [`CARGO_BUILD_TARGET`](https://doc.rust-lang.org/cargo/reference/config.html#buildtarget) 环境变量，其值将追加到文件名的主干部分。

如果设置了 `PYAPP_REPO` 环境变量，则构建过程将在该目录中本地执行，而不是从 [crates.io](https://crates.io) 安装。请注意，如果 `CARGO` 环境变量指向的是 [cross](https://github.com/cross-rs/cross)，那么这是[必需的](https://github.com/cross-rs/cross/issues/1215)。
