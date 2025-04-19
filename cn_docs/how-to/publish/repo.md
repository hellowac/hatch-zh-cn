# 如何配置索引发布的仓库

----

您可以通过 `-r`/`--repo` 选项或设置 `HATCH_INDEX_REPO` 环境变量来选择上传目标仓库。

无需直接指定完整 URL，而可以在 Hatch 的 [配置文件](../../config/hatch.md) 中通过 `publish.index.repos` 表定义命名仓库。例如：

```toml tab="config.toml"
[publish.index.repos.private]
url = "..."
...
```

以下仓库名称由 Hatch 保留，不能被自定义覆盖：

| 名称 | 仓库地址 |
| --- | --- |
| `main` | https://upload.pypi.org/legacy/ |
| `test` | https://test.pypi.org/legacy/ |

默认使用 `main` 仓库。