# 如何配置依赖解析

-----

大多数 Hatch 环境类型（如默认的 [virtual](../../plugins/environment/virtual.md)）使用的是 [pip](https://github.com/pypa/pip) 来安装依赖项。因此，您可以使用标准的 [pip 环境变量](https://pip.pypa.io/en/stable/topics/configuration/#environment-variables) 来影响 pip 的行为。

以下示例展示如何为 [默认环境](../../config/environment/overview.md#inheritance) 设置两个私有索引（使用 [上下文格式化](../../config/context.md#environment-variables) 实现身份验证），最后回退到 PyPI：

```toml config-example
[tool.hatch.envs.default.env-vars]
PIP_INDEX_URL = "https://token:{env:GITLAB_API_TOKEN}@gitlab.com/api/v4/groups/<group1_path>/-/packages/pypi/simple/"
PIP_EXTRA_INDEX_URL = "https://token:{env:GITLAB_API_TOKEN}@gitlab.com/api/v4/groups/<group2_path>/-/packages/pypi/simple/ https://pypi.org/simple/"
```

## 使用 UV 时

如果您正在[使用 UV](select-installer.md)，需要使用 UV 支持的另一组 [环境变量](https://github.com/astral-sh/uv/tree/0.1.35#environment-variables) 来配置其行为。上述示例在 UV 下的等效写法如下：

```toml config-example
[tool.hatch.envs.default.env-vars]
UV_EXTRA_INDEX_URL = "https://token:{env:GITLAB_API_TOKEN}@gitlab.com/api/v4/groups/<group1_path>/-/packages/pypi/simple/"
UV_INDEX_URL = "https://token:{env:GITLAB_API_TOKEN}@gitlab.com/api/v4/groups/<group2_path>/-/packages/pypi/simple/ https://pypi.org/simple/"
```

!!! tip
    如果您需要对软件包索引的优先级顺序进行精确控制，建议使用 UV，因为 `pip` 并不保证 [索引的顺序](https://github.com/pypa/pip/issues/8606)。