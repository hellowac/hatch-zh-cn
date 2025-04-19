# Hatch

<div class="grid" markdown>

![Hatch logo](assets/images/logo.svg){ role="img" }

| | |
| --- | --- |
| 持续集成/交付(CI/CD) | [![CI - Test](https://github.com/pypa/hatch/actions/workflows/test.yml/badge.svg){ loading=lazy .off-glb }](https://github.com/pypa/hatch/actions/workflows/test.yml) [![CD - Build Hatch](https://github.com/pypa/hatch/actions/workflows/build-hatch.yml/badge.svg){ loading=lazy .off-glb }](https://github.com/pypa/hatch/actions/workflows/build-hatch.yml) [![CD - Build Hatchling](https://github.com/pypa/hatch/actions/workflows/build-hatchling.yml/badge.svg){ loading=lazy .off-glb }](https://github.com/pypa/hatch/actions/workflows/build-hatchling.yml) |
| 文档(Docs) | [![Docs - Release](https://github.com/pypa/hatch/actions/workflows/docs-release.yml/badge.svg){ loading=lazy .off-glb }](https://github.com/pypa/hatch/actions/workflows/docs-release.yml) [![Docs - Dev](https://github.com/pypa/hatch/actions/workflows/docs-dev.yml/badge.svg){ loading=lazy .off-glb }](https://github.com/pypa/hatch/actions/workflows/docs-dev.yml) |
| 包(Package) | [![PyPI - Version](https://img.shields.io/pypi/v/hatch.svg?logo=pypi&label=PyPI&logoColor=gold){ loading=lazy .off-glb }](https://pypi.org/project/hatch/) [![PyPI - Python Version](https://img.shields.io/pypi/pyversions/hatch.svg?logo=python&label=Python&logoColor=gold){ loading=lazy .off-glb }](https://pypi.org/project/hatch/) [![PyPI - Installs](https://img.shields.io/pypi/dm/hatchling.svg?color=blue&label=Installs&logo=pypi&logoColor=gold){ loading=lazy .off-glb }](https://pypi.org/project/hatch/) [![Release - Downloads](https://img.shields.io/github/downloads/pypa/hatch/total?label=Downloads){ loading=lazy .off-glb }](https://github.com/pypa/hatch/releases) |
| 元数据(Meta) | [![Hatch project](https://img.shields.io/badge/%F0%9F%A5%9A-Hatch-4051b5.svg){ loading=lazy .off-glb }](https://github.com/pypa/hatch) [![linting - Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json){ loading=lazy .off-glb }](https://github.com/astral-sh/ruff) [![types - Mypy](https://img.shields.io/badge/types-Mypy-blue.svg){ loading=lazy .off-glb }](https://github.com/python/mypy) [![License - MIT](https://img.shields.io/badge/license-MIT-9400d3.svg){ loading=lazy .off-glb }](https://spdx.org/licenses/) [![GitHub Sponsors](https://img.shields.io/github/sponsors/ofek?logo=GitHub%20Sponsors&style=social){ loading=lazy .off-glb }](https://github.com/sponsors/ofek) |

</div>

-----

Hatch 是一款现代、可扩展的 Python 项目管理器。参见 [为何选择 Hatch？](why.md) 页面以了解更多信息。

<div class="grid cards" markdown>

-   :material-hammer-wrench:{ .lg .middle } __构建系统__

    ---

    默认实现可复现构建，并拥有丰富的插件生态

    [:octicons-arrow-right-24: 配置构建](config/build.md#build-system)

-   :material-lock:{ .lg .middle } __环境__

    ---

    稳健的环境管理，支持自定义脚本与 UV

    [:octicons-arrow-right-24: 入门指南](environment.md)

-   :material-language-python:{ .lg .middle } __Python 管理__

    ---

    可在环境中选择手动安装或自动管理 Python

    [:octicons-arrow-right-24: 尝试一下](tutorials/python/manage.md)

-   :octicons-shield-check-24:{ .lg .middle } __测试__

    ---

    基于最佳实践的测试执行方案

    [:octicons-arrow-right-24: 运行测试](tutorials/testing/overview.md)

-   :material-magnify-scan:{ .lg .middle } __静态分析__

    ---

    由 Ruff 提供支持，默认配置合理、始终保持最新

    [:octicons-arrow-right-24: 了解详情](config/internal/static-analysis.md)

-   :material-console-line:{ .lg .middle } __脚本运行器__

    ---

    可按指定依赖与 Python 版本运行脚本

    [:octicons-arrow-right-24: 执行脚本](how-to/run/python-scripts.md)

-   :material-publish:{ .lg .middle } __发布__

    ---

    轻松上传至 PyPI 或其他索引仓库

    [:octicons-arrow-right-24: 查看方式](publish.md)

-   :octicons-number-24:{ .lg .middle } __版本控制__

    ---

    流畅的版本升级工作流

    [:octicons-arrow-right-24: 管理版本](version.md)

-   :octicons-project-template-24:{ .lg .middle } __项目生成__

    ---

    基于模板创建新项目，符合最佳实践

    [:octicons-arrow-right-24: 项目设置](intro.md#setup)

-   :material-speedometer:{ .lg .middle } __高效 CLI__

    ---

    Hatch 的速度最多可达同类工具的 3 倍

    [:octicons-arrow-right-24: CLI 参考](cli/about.md)

</div>

## 许可证

Hatch 遵循 [MIT](https://spdx.org/licenses/MIT.html) 许可证分发。

## 导航说明

你可以通过每个页面顶部的下拉菜单选择特定 `MAJOR.MINOR` 版本的文档。`dev` 版本表示尚未发布的最新变更。

此外，桌面端用户可使用以下快捷键：

| 按键 | 操作 |
| --- | --- |
| <ul><li><kbd>,</kbd>（逗号）</li><li><kbd>p</kbd></li></ul> | 跳转至“上一页” |
| <ul><li><kbd>.</kbd>（句点）</li><li><kbd>n</kbd></li></ul> | 跳转至“下一页” |
| <ul><li><kbd>/</kbd></li><li><kbd>s</kbd></li></ul> | 打开搜索窗口 |

