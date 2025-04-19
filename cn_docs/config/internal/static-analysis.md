# 静态分析配置

-----

[`fmt`](../../cli/reference.md#hatch-fmt) 命令执行的静态分析（[默认情况下](#customize-behavior)）完全由 [Ruff](https://github.com/astral-sh/ruff) 驱动。

Hatch 提供了[默认设置](#default-settings)，用户配置可以对其进行[扩展](#extending-config)。

## 扩展配置（Extending config）

在定义配置时，请务必使用以 `extend-` 为前缀的选项，例如 [`extend-select`](https://docs.astral.sh/ruff/settings/#extend-select)。示例如下：

=== ":octicons-file-code-16: pyproject.toml"

```toml
[tool.ruff.format]
preview = true
quote-style = "single"

[tool.ruff.lint]
preview = true
extend-select = ["C901"]

[tool.ruff.lint.extend-per-file-ignores]
"docs/.hooks/*" = ["INP001", "T201"]

[tool.ruff.lint.isort]
known-first-party = ["foo", "bar"]
```

=== ":octicons-file-code-16: ruff.toml"

```toml
[format]
preview = true
quote-style = "single"

[lint]
preview = true
extend-select = ["C901"]

[lint.extend-per-file-ignores]
"docs/.hooks/*" = ["INP001", "T201"]

[lint.isort]
known-first-party = ["foo", "bar"]
```

!!! note
    如果没有启用[持久化配置](#persistent-config)，则无需显式地[扩展](https://docs.astral.sh/ruff/settings/#extend)默认值，因为 Hatch 会自动处理。

## 持久化配置（Persistent config）

如果你希望将默认配置保存在项目中，可通过如下方式设置显式路径：

```toml config-example
[tool.hatch.envs.hatch-static-analysis]
config-path = "ruff_defaults.toml"
```

然后在 Ruff 的配置中将其视为默认文件的扩展：

=== ":octicons-file-code-16: pyproject.toml"

```toml
[tool.ruff]
extend = "ruff_defaults.toml"
```

=== ":octicons-file-code-16: ruff.toml"

```toml
extend = "ruff_defaults.toml"
```

每当你希望更新默认配置（如升级 Hatch 后），都必须使用 `--sync` 标志运行一次 [`fmt`](../../cli/reference.md#hatch-fmt) 命令，例如：

```
hatch fmt --check --sync
```

!!! tip
    推荐使用该方法，因为它允许其他工具（如 IDE）使用默认配置。

### 不使用配置（No config）

如果你不希望 Hatch 使用任何默认配置，而完全依赖你自己的配置，可以将路径设置为任意值，然后在 Ruff 配置中不使用 `extend`：

```toml config-example
[tool.hatch.envs.hatch-static-analysis]
config-path = "none"
```

## 自定义行为（Customize behavior）

你可以完全修改 [`fmt`](../../cli/reference.md#hatch-fmt) 命令所使用环境的行为。请参见该[操作指南](../../how-to/static-analysis/behavior.md)，了解详细示例。

### 依赖项

通过显式定义环境[依赖项](../environment/overview.md#dependencies)，可锁定特定版本的 Ruff：

```toml config-example
[tool.hatch.envs.hatch-static-analysis]
dependencies = ["ruff==X.Y.Z"]
```

### 脚本

如果你希望更改默认执行的命令，可以重写 [scripts](../environment/overview.md#scripts)。以下四个脚本必须定义：

```toml config-example
[tool.hatch.envs.hatch-static-analysis.scripts]
format-check = "..."
format-fix = "..."
lint-check = "..."
lint-fix = "..."
```

`format-*` 脚本对应 `--formatter`/`-f` 标志，而 `lint-*` 脚本对应 `--linter`/`-l` 标志。`*-fix` 脚本为默认执行脚本，`*-check` 脚本在使用 `--check` 标志时执行。

!!! note "提示"
    如果你选择使用其他静态分析工具，请确保同步更新相应的[依赖项](#dependencies)。

### 安装器（Installer）

默认情况下，[UV 是启用的](../../how-to/environment/select-installer.md)。你可以通过以下方式禁用：

```toml config-example
[tool.hatch.envs.hatch-static-analysis]
installer = "pip"
```

## 默认设置（Default settings）

### 非规则相关设置（Non-rule settings）

- [行宽](https://docs.astral.sh/ruff/settings/#line-length) 设置为 120  
- 启用 [文档字符串格式化](https://docs.astral.sh/ruff/formatter/#docstring-formatting)，其中 [行宽](https://docs.astral.sh/ruff/settings/#format_docstring-code-line-length) 设置为 80  
- 仅允许使用绝对导入（[详见](https://docs.astral.sh/ruff/settings/#lint_flake8-tidy-imports_ban-relative-imports)），[测试除外](#per-file-ignored-rules)  
- 归一化后的[项目名称](../metadata.md#name)被视为[一方代码](https://docs.astral.sh/ruff/settings/#lint_isort_known-first-party)的导入项  

### 按文件忽略规则（Per-file ignored rules）

<HATCH_RUFF_PER_FILE_IGNORED_RULES>

### 启用规则（Selected rules）

以下规则基于 Ruff 的 <HATCH_RUFF_VERSION> 版本。带有 ^P^ 的规则仅在启用 [preview 模式](https://docs.astral.sh/ruff/preview/)时选中。

共启用了 <HATCH_RUFF_STABLE_RULES_COUNT> 个稳定规则 和 <HATCH_RUFF_PREVIEW_RULES_COUNT> 个 preview 规则。

<HATCH_RUFF_SELECTED_RULES>

#### 未启用的规则（Unselected）

共有 <HATCH_RUFF_UNSELECTED_RULES_COUNT> 个未启用的规则。

<HATCH_RUFF_UNSELECTED_RULES>
