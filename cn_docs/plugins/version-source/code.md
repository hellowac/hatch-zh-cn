# 代码版本源

-----

## 更新

不支持设置版本。

## 配置

版本源插件的名称是 `code`。

```toml config-example
[tool.hatch.version]
source = "code"
```

## 选项

| 选项 | 描述 |
| ---- | ---- |
| `path`（必填） | 将加载的 Python 文件或扩展模块的相对路径 |
| `expression` | 一个 Python 表达式，当在加载的文件上下文中评估时返回版本。默认表达式是 `__version__`。 |
| `search-paths` | 一组相对路径列表，将被添加到 Python 的搜索路径前面 |

## 缺失的导入

如果选择的路径导入了项目中的另一个模块，则需要使用绝对导入并配合 `search-paths` 选项。例如，假设您需要加载以下文件：

```python tab="src/pkg/__init__.py"
    from ._version import get_version

    __version__ = get_version()
```

您应该将其更改为：

```python tab="src/pkg/__init__.py"
    from pkg._version import get_version

    __version__ = get_version()
```

然后，配置将变为：

```toml config-example
[tool.hatch.version]
source = "code"
path = "src/pkg/__init__.py"
search-paths = ["src"]
```