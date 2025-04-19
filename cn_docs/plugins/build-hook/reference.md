# 构建钩子插件（Build hook plugins）

-----

构建钩子提供了在构建过程的各个阶段执行的代码。有关 [构建钩子配置](../../config/build.md#build-hooks)的更多信息，请参阅文档。

## 知名第三方插件（Known third-party）

- [hatch-argparse-manpage](https://github.com/damonlynch/hatch-argparse-manpage) - 为基于 [argparse](https://docs.python.org/3/library/argparse.html#argparse.ArgumentParser) 的 CLI 生成手册页
- [hatch-autorun](https://github.com/ofek/hatch-autorun) - 用于向安装中注入代码，自动在第一次导入之前运行
- [hatch-build-scripts](https://github.com/rmorshea/hatch-build-scripts) - 运行任意的 shell 命令，生成构建产物
- [hatch-cython](https://github.com/joshua-auchincloss/hatch-cython) - 构建 [Cython](https://github.com/cython/cython) 扩展
- [hatch-gettext](https://github.com/damonlynch/hatch-gettext) - 使用 GNU `gettext` 工具编译多语言消息
- [hatch-jupyter-builder](https://github.com/jupyterlab/hatch-jupyter-builder) - 用于 Jupyter 项目生态系统中的包
- [hatch-mypyc](https://github.com/ofek/hatch-mypyc) - 使用 [Mypyc](https://github.com/mypyc/mypyc) 编译代码
- [hatch-odoo](https://github.com/acsone/hatch-odoo) - 将 Odoo 插件打包到适当的命名空间中
- [scikit-build-core](https://github.com/scikit-build/scikit-build-core) - 使用 CMake 构建扩展模块

## 概述（Overview）

构建钩子会在每个选定的构建目标 [版本](../../config/build.md#versions) 中运行。

[初始化](#hatchling.builders.hooks.plugin.interface.BuildHookInterface.initialize) 阶段会在每次构建之前立即执行，而 [终结](#hatchling.builders.hooks.plugin.interface.BuildHookInterface.finalize) 阶段则在每次构建之后立即执行。每个阶段都有机会查看或修改 [构建数据](#build-data)。

## 构建数据（Build data）

构建数据是一个简单的映射，其内容可以影响构建行为。哪些字段存在并被识别取决于每个构建目标。

以下字段始终存在并由构建系统本身识别：

| 字段 | 类型 | 描述 |
| --- | --- | --- |
| `artifacts` | `#!python list[str]` | 这是一个额外的 [`artifact` 模式](../../config/build.md#artifacts) 列表，通常只应附加内容 |
| `force_include` | `#!python dict[str, str]` | 这是一个额外的 [强制包含路径](../../config/build.md#forced-inclusion) 映射，若发生冲突，则优先使用此映射 |
| `build_hooks` | `#!python tuple[str, ...]` | 这是已配置构建钩子的名称的不可变序列，并与它们的执行顺序一致 |

!!! attention
    虽然用户-facing TOML 选项使用连字符（hyphenated），但构建数据字段应使用下划线命名，以便插件可以将其用作有效的 Python 标识符。

## 注意事项（Notes）

在某些情况下，可能需要使用 `force_include` 而不是 `artifacts`。例如，假设你希望将 `lib.so` 直接安装到 `site-packages` 的根目录下，而项目定义了一个名为 `src/foo` 的 [包](../../config/build.md#packages)。如果你创建 `src/lib.so`，则不会匹配，因为目录遍历是从 `src/foo` 开始的，而不是从 `src` 开始。在这种情况下，必须做以下操作之一：

```python
build_data['force_include']['src/lib.so'] = 'src/lib.so'
```

或者

```python
build_data['force_include']['/absolute/path/to/src/lib.so'] = 'src/lib.so'
```

::: hatchling.builders.hooks.plugin.interface.BuildHookInterface
    选项（options）:
      成员（members）:
      - PLUGIN_NAME
      - app
      - root
      - config
      - build_config
      - target_name
      - directory
      - dependencies
      - clean
      - initialize
      - finalize
