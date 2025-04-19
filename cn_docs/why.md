# 为什么选择 Hatch？

-----

从高层次来看，Hatch 的价值主张在于：如果用户采用其全部功能，那么许多其他工具将变得不再必要，因为 Hatch 支持几乎所有可能的需求。同时，即使用户只使用其中某些特性，相比其他工具仍具有明显优势。

## 构建后端

Hatchling 是 [构建后端](config/build.md#build-system) 的姊妹项目，相较于大家熟悉的 [setuptools](https://github.com/pypa/setuptools)，它具有许多优势。下面仅将其与 setuptools 对比：

- **更合理的默认值：** setuptools 的默认行为通常并不适用于大多数用户。
    - 对于源码发行包（source distribution），setuptools 使用自定义的文件枚举逻辑决定默认包含/排除哪些文件；而 Hatchling 则根据版本控制系统（如 Git 的 `.gitignore` 文件）提供的 [默认值](plugins/builder/sdist.md#default-file-selection)。
    - 对于 wheel 包，setuptools 会尝试找出所有看起来像 Python 包的目录，这可能导致诸如测试或工具目录等不该发布的内容被意外包含。Hatchling 的 [默认行为](plugins/builder/wheel.md#default-file-selection) 更为谨慎，仅在满足特定启发式判断时才包含，并在判断失败时抛出错误。
- **易于配置：** Hatchling 的设计吸收了配置 setuptools 的种种痛点。
    - Hatchling 在各个配置选项中均 [使用](config/build.md#patterns) Git 自身的 glob 模式，这是用户最熟悉的语法。而 setuptools 在源码包和 wheel 包中分别使用 shell 风格的 glob 模式与 Python 包路径语法，容易混淆。
    - 配置源码包的包含内容通常需要额外使用 [`MANIFEST.in`](https://setuptools.pypa.io/en/latest/userguide/miscellaneous.html#using-manifest-in) 文件，并使用其专有语法与指令。用户不仅需学习该语法，还需理解主文件（如 `setup.py`）中的哪些选项会在何种条件下影响行为。而 Hatchling 的所有配置都集中在单个文件中，并通过如 `[tool.hatch.build.targets.wheel]` 等明确的段落分类。
    - 默认情况下，wheel 包会排除非 Python 文件，若想包含这些文件，通常需为每个嵌套目录定义冗长的规则。而 Hatchling 不区分文件类型，更像通用构建系统，行为直观。
- **可编辑安装（editable installs）：** Hatchling 的默认行为支持 IDE 等外部工具进行静态分析；而 setuptools 需要 [额外配置](https://setuptools.pypa.io/en/latest/userguide/development_mode.html#legacy-behavior)，否则例如在 Visual Studio Code 中将无法进行自动补全。此外，该特性被标为“legacy”，未来可能被移除。
- **可复现性（Reproducibility）：** Hatchling 默认构建具有可复现性（reproducible）的 wheel 和源码包，而 setuptools [并不支持](https://github.com/pypa/setuptools/issues/2133) 对源码包的可复现性，且也无法保证 wheel 可复现。
- **可扩展性（Extensibility）：** 虽然 setuptools 支持 [扩展](https://setuptools.pypa.io/en/latest/userguide/extension.html)，但其 API 较底层，开发成本高。而 Hatchling 引入了 [插件](https://hatch.pypa.io/latest/plugins/about/) 概念，不同插件类型职责明确，API 更加简洁，开发体验更友好。

***为什么不选择 Hatchling？***

若你的项目涉及构建扩展模块（extension modules），建议继续使用 setuptools 或其他专门支持编译器集成的构建后端。

## 环境管理

本节将 Hatch 与 `tox` 和 `nox` 进行对比。高层次上，Hatch 有以下共通优势：

- **Python 版本管理：** Hatch 能在请求的 Python 版本不存在时自动 [下载 Python 发行版](plugins/environment/virtual.md#internal-distributions)，而其他工具则会报错，并仅提供忽略该错误的选项。
- **理念差异：** `tox` 和 `nox` 将环境主要作为“可执行单元”：为一组依赖绑定某个执行动作。这类似 Dockerfile 中定义 `CMD` 指令，但缺乏运行时灵活性。通常需要针对略有不同的任务定义多个环境，既占用磁盘又增加配置复杂性，用户执行时也容易混淆。

    Hatch 则将 [环境](environment.md) 视为可供执行任意命令的隔离空间。你可以在同一个测试环境中定义多个具名 [脚本](config/environment/overview.md#scripts)，比如分别运行单元测试与集成测试。每个命令可命名为你喜欢的方式，自定义程度更高。此外，你还可以 [进入环境 shell](environment.md#entering-environments)，执行子进程自动切换至目标 shell，并自动配置 PATH、变更提示符等。

- **配置方式：**
    - `nox` 使用 Python 文件定义配置，虽灵活但冗长，学习成本高；Hatch 使用标准化格式，行为明确，易于上手。
- **扩展机制：**
    - `tox` 虽然支持 [插件扩展](https://tox.wiki/en/4.11.4/plugins_api.html)，但其 API 过于底层且紧耦合内部结构，开发插件难度大。可参考 [此插件迁移案例](https://github.com/DataDog/integrations-core/blob/4eb2a1d530bcf810542cf9e45b48fadc7057301c/datadog_checks_dev/datadog_checks/dev/plugin/hatch/environment_collector.py#L100-L148)。
    - `nox` 允许在本地项目中任意使用 Python，但缺乏独立插件机制。若需扩展，通常需封装 `nox` 并暴露定制化 API（[示例](https://github.com/cjolowicz/nox-poetry)）。

***为什么不选择 Hatch？***

如果你正在使用 `nox` 并依赖其 `notify` 机制（[文档说明](https://nox.thea.codes/en/stable/config.html#nox.sessions.Session.notify)），那么迁移到 Hatch 不属于直接转换，可能需要重新设计逻辑结构。

## Python 版本管理

此处比较的是 Hatch 与 [pyenv](https://github.com/pyenv/pyenv) 的 [Python 管理方式](tutorials/python/manage.md)：

- ***跨平台：*** Hatch 提供完全一致的用户体验，而 `pyenv` 不支持 Windows，需使用另一个 [独立项目](https://github.com/pyenv-win/pyenv-win) 来模拟功能。
- ***无构建依赖：*** Hatch 所有 [可用发行版](cli/reference.md#hatch-python-show) 均为预构建版本，无需本地构建环境。而 pyenv 安装需依赖精确的 [构建依赖](https://github.com/pyenv/pyenv/wiki#suggested-build-environment)，根据平台和 Python 版本可能差异较大。预构建版本的另一优势是安装极快，仅需下载和解压。
- ***默认优化：*** Hatch 所提供的 [CPython 发行版](plugins/environment/virtual.md#cpython) 都启用了 PGO（Profile Guided Optimization）与 LTO（Link-Time Optimization），可实现 10-30% 性能提升。这些构建已广泛被采用，例如 Bazel 构建系统也使用相同的发行版。
- ***简洁性：*** Hatch 将 Python 安装视为普通目录，用户只需将其加入 PATH，也可自定义安装路径。相比之下，pyenv 使用 [shim 层](https://github.com/pyenv/pyenv/tree/74a2523c97d2e5c1dbdca7b58f3372324ccad4e6#understanding-shims)，通过代理包装实际二进制文件。该机制带来多种负面影响：
    - 用户需手动管理当前激活的 Python 版本，命令行切换繁琐，且需对全局与项目局部版本的作用域有清晰认知，使用体验不一致。Hatch 则不直接使用全局安装版本，而是根据 PATH 中存在的版本自动创建兼容虚拟环境。
    - 每种 shell 都需额外配置以正确初始化 pyenv，导致某些无 shell 的进程行为不一致。
    - 排查 Python 搜索路径问题异常困难。许多用户遇到“代码来自意料之外的位置”这类问题，几乎都是 pyenv 对 PATH 的影响所致。

***为什么不选择 Hatch？***

Hatch 当前仅支持以“次版本号”为粒度安装 Python，例如 3.12，而无法选择精确补丁版本（如 3.12.3）。若你对特定补丁版本有要求，请使用其他安装机制。
