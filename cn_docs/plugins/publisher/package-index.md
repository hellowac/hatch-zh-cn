# 索引发布者

-----

请参阅 [发布](../../publish.md) 文档。

## 选项

| 标志 | 配置名称  | 描述                                         |
| ---- | --------- | -------------------------------------------- |
| `-r`/`--repo`   | `repo`    | 用于发布构件的仓库                             |
| `-u`/`--user`   | `user`    | 用于认证的用户                                 |
| `-a`/`--auth`   | `auth`    | 用于认证的凭证                                 |
| `--ca-cert`     | `ca-cert` | CA 包的路径                                    |
| `--client-cert` | `client-cert` | 客户端证书的路径，可选地包含私钥                  |
| `--client-key`  | `client-key`  | 客户端证书私钥的路径                             |
|      | `repos`   | 一个命名的 [repositories](#repositories) 表，包含各自的选项 |

## 配置

发布者插件的名称是 `index`。

```toml tab="config.toml"
[publish.index]
```

### 仓库

可以使用 `repos` 表为每个仓库重写所有顶层选项，并要求每个仓库具有一个 `url` 属性。以下是默认配置：

```toml tab="config.toml"
[publish.index.repos.main]
url = "https://upload.pypi.org/legacy/"

[publish.index.repos.test]
url = "https://test.pypi.org/legacy/"
```

`repo` 和 `repos` 选项无效。

### 确认提示

您可以要求启用确认提示或使用 `-y`/`--yes` 标志，方法是在 Hatch 的 [配置文件](../../config/hatch.md) 或项目特定配置中设置发布者的 `disable` 选项（项目配置优先）：

```toml tab="config.toml"
[publish.index]
disable = true
```

```toml config-example
[tool.hatch.publish.index]
disable = true
```