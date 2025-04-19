# 如何运行 Python 脚本

-----

[`run`](../../cli/reference.md#hatch-run) 命令支持运行带有 [内联元数据](https://packaging.python.org/en/latest/specifications/inline-script-metadata/) 的 Python 脚本，此类脚本会自动创建一个专用的 [环境](../../config/environment/overview.md)，并安装所需依赖项，同时使用合适的 Python 版本。

脚本元数据块是以 `# /// script` 开始、以 `# ///` 结束的注释块。两者之间的每一行都必须是以 `#` 开头的注释，并且在去除注释字符后，必须是一个合法的 [TOML](https://github.com/toml-lang/toml) 文档。

支持的顶层字段包括：

- `dependencies`：字符串列表，指定该脚本的运行时依赖。每项必须是合法的 [依赖规范](https://packaging.python.org/en/latest/specifications/dependency-specifiers/#dependency-specifiers)。
- `requires-python`：字符串，指定该脚本兼容的 Python 版本，值必须是合法的 [版本规范](https://packaging.python.org/en/latest/specifications/version-specifiers/#version-specifiers)。

以下是一个带有合法元数据块的 Python 脚本示例：

```python tab="script.py"
# /// script
# requires-python = ">=3.11"
# dependencies = [
#   "httpx",
#   "rich",
# ]
# ///

import httpx
from rich.pretty import pprint

resp = httpx.get("https://peps.python.org/api/peps.json")
data = resp.json()
pprint([(k, v["title"]) for k, v in data.items()][:10])
```

直接运行它：

```
$ hatch run /path/to/script.py
Creating environment: SyB4bPbL
Checking dependencies
Syncing dependencies
[
│   ('1', 'PEP Purpose and Guidelines'),
│   ('2', 'Procedure for Adding New Modules'),
│   ('3', 'Guidelines for Handling Bug Reports'),
│   ('4', 'Deprecation of Standard Modules'),
│   ('5', 'Guidelines for Language Evolution'),
│   ('6', 'Bug Fix Releases'),
│   ('7', 'Style Guide for C Code'),
│   ('8', 'Style Guide for Python Code'),
│   ('9', 'Sample Plaintext PEP Template'),
│   ('10', 'Voting Guidelines')
]
```

!!! note "说明"
    - 示例中显示的提示信息仅会在首次运行时临时出现在终端中。
    - 尽管环境名称基于脚本的绝对路径生成，但命令行参数不要求使用绝对路径。

## 环境配置

您也可以通过 `[tool.hatch]` 表来直接控制脚本的 [环境](../../config/environment/overview.md)。例如，如果希望禁用默认启用的 UV 安装器（见 [启用 UV](../environment/select-installer.md#enabling-uv)），可添加如下配置：

```python tab="script.py"
# /// script
# ...
# [tool.hatch]
# installer = "pip"
# ///
```
