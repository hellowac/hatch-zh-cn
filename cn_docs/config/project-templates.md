# 项目模板（Project templates）

-----

你可以通过 Hatch 的[配置文件](hatch.md)控制 [`new`](../cli/reference.md#hatch-new) 命令创建新项目的方式。

## 作者信息（Author）

```toml tab="config.toml"
[template]
name = "..."
email = "..."
```

## 许可证（Licenses）

```toml tab="config.toml"
[template.licenses]
headers = true
default = [
  "MIT",
]
```

许可证列表应由 [SPDX 标识符](https://spdx.org/licenses/)组成。如果指定了多个许可证，它们将被放入一个 [LICENSES](https://reuse.software/faq/#multi-licensing) 目录中。

## 选项（Options）

### 测试（Tests）

此选项会添加一个 `tests` 目录，并创建用于测试和静态分析的环境。

```toml tab="config.toml"
[template.plugins.default]
tests = true
```

### 持续集成（CI）

此选项会添加一个 [GitHub Actions 工作流](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#workflows)，使用多个平台与现代 Python 版本运行测试。

```toml tab="config.toml"
[template.plugins.default]
ci = false
```

### `src` 布局

参见[这篇博客文章](https://blog.ionelmc.ro/2014/05/25/python-packaging/)了解更多信息。

```toml tab="config.toml"
[template.plugins.default]
src-layout = true
```

## 功能标志（Feature flags）

### 命令行接口（Command line interface）

`--cli` 标志会添加一个基于 [Click](https://github.com/pallets/click) 的 CLI，并支持通过 `python -m <PKG_NAME>` 方式调用。