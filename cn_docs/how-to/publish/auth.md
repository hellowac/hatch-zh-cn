# 如何进行索引发布时的身份验证

----

用户名的来源，按优先级如下：

1. `--user` / `-u` 命令行选项；
2. `HATCH_INDEX_USER` 环境变量；
3. [`repos` 表](../../plugins/publisher/package-index.md)；
4. [`~/.pypirc` 文件](https://packaging.python.org/en/latest/specifications/pypirc/)；
5. 交互式提示输入。

若以上均未提供，则回退使用 `__token__` 作为用户名。

密码的获取顺序如下：

1. 若用户名来自 `~/.pypirc` 文件，则从其中读取密码；
2. `--auth` / `-a` 命令行选项；
3. `HATCH_INDEX_AUTH` 环境变量；
4. [`repos` 表](../../plugins/publisher/package-index.md)；
5. 通过 [keyring](https://github.com/jaraco/keyring) 支持的操作系统级凭据服务；
6. 交互式提示输入。

如果使用交互方式输入的凭据，用户名将存储于 [Hatch 缓存](../../config/hatch.md#cache)，密码将存储在可用的 [keyring](https://github.com/jaraco/keyring) 支持的凭据存储中。

如需自动将项目发布到 PyPI，推荐使用 [OIDC 的“可信发布”机制](https://docs.pypi.org/trusted-publishers/)（例如 PyPA 提供的 [`pypi-publish`](https://github.com/pypa/gh-action-pypi-publish) GitHub Action）或使用每个项目的 [API token](https://pypi.org/help/#apitoken)。
