# 自定义静态分析行为

-----

可以通过修改保留的 [环境配置](../../config/internal/static-analysis.md#customize-behavior) `hatch-static-analysis`，完全控制 [`fmt`](../../cli/reference.md#hatch-fmt) 命令执行的静态分析行为。例如，以下配置将默认行为替换为结合使用 [Black](https://github.com/psf/black)、[isort](https://github.com/PyCQA/isort) 和基础的 [flake8](https://github.com/PyCQA/flake8)：

```toml config-example
[tool.hatch.envs.hatch-static-analysis]
dependencies = ["black", "flake8", "isort"]

[tool.hatch.envs.hatch-static-analysis.scripts]
format-check = [
  "black --check --diff {args:.}",
  "isort --check-only --diff {args:.}",
]
format-fix = [
  "isort {args:.}",
  "black {args:.}",
]
lint-check = "flake8 {args:.}"
lint-fix = "lint-check"
```

- `format-*` 脚本对应 `--formatter` / `-f` 选项。
- `lint-*` 脚本对应 `--linter` / `-l` 选项。
- 默认运行的是 `*-fix` 脚本。
- `--check` 标志对应 `*-check` 脚本。

基于上述配置，以下命令行为及其展开脚本如下：

| 命令 | 展开脚本 |
| --- | --- |
| `hatch fmt` | <ul><li><code>flake8 .</code></li><li><code>isort .</code></li><li><code>black .</code></li></ul> |
| `hatch fmt src tests` | <ul><li><code>flake8 src tests</code></li><li><code>isort src tests</code></li><li><code>black src tests</code></li></ul> |
| `hatch fmt -f` | <ul><li><code>isort .</code></li><li><code>black .</code></li></ul> |
| `hatch fmt -l` | <ul><li><code>flake8 .</code></li></ul> |
| `hatch fmt --check` | <ul><li><code>flake8 .</code></li><li><code>black --check --diff .</code></li><li><code>isort --check-only --diff .</code></li></ul> |
| `hatch fmt --check -f` | <ul><li><code>black --check --diff .</code></li><li><code>isort --check-only --diff .</code></li></ul> |
| `hatch fmt --check -l` | <ul><li><code>flake8 .</code></li></ul> |