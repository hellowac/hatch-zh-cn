# 常见问题解答（FAQ）

-----

## 互操作性（Interoperability）

***问***：存在被锁定在该工具中的风险吗？

***答***：几乎没有！除了 [插件系统](../plugins/about.md) 之外，其它所有内容默认都遵循 Python 的既定标准。项目元数据完全基于[标准规范][project metadata standard]，构建系统兼容 [PEP 517][] / [PEP 660][]，版本控制使用 [PEP 440](https://peps.python.org/pep-0440/#public-version-identifiers) 指定的方案，依赖项使用 [PEP 508][] 字符串定义，环境则使用 [virtualenv](https://github.com/pypa/virtualenv)。

***问***：必须使用所有功能吗？

***答***：不，所有功能都是可选的！你可以[仅使用构建系统](../build.md#packaging-ecosystem)、发布由其他工具构建的 wheel 和源码分发包、仅使用环境管理等。

## 库 vs 应用程序

***问***：是否同时支持库和应用程序的工作流？

***答***：基本支持！应用程序可以像库一样使用环境管理，插件可用于以任意格式 [构建](../plugins/builder/reference.md) 项目，或将构建产物 [发布](../plugins/publisher/reference.md) 到任意位置。

唯一的限制是，目前尚不支持基于依赖集以可重现方式重新创建环境。虽然由于 [PEP 665][] 被拒绝，标准锁文件格式仍遥遥无期，但 [pip 的解析功能正在实现中](https://github.com/pypa/pip/pull/10748)。一旦稳定，Hatch 将添加锁定功能，并提供专门的应用管理文档。

## 工具迁移

***问***：如何迁移到 Hatch？

### 构建系统

=== "Setuptools"
```python tab="setup.py"
import os
from io import open

from setuptools import find_packages, setup

about = {}
with open(os.path.join('src', 'foo', '__about__.py'), 'r', 'utf-8') as f:
    exec(f.read(), about)

with open('README.md', 'r', 'utf-8') as f:
    readme = f.read()

setup(
    # 元数据
    name='foo',
    version=about['__version__'],
    description='...',
    long_description=readme,
    long_description_content_type='text/markdown',
    author='...',
    author_email='...',
    project_urls={
        'Documentation': '...',
        'Source': '...',
    },
    classifiers=[
        '...',
    ],
    keywords=[
        '...',
    ],
    python_requires='>=3.8',
    install_requires=[
        '...',
    ],
    extras_require={
        'feature': ['...'],
    },

    # 打包
    packages=find_packages(where='src'),
    package_dir={'': 'src'},
    package_data={
        'foo': ['py.typed'],
    },
    zip_safe=False,
    entry_points={
        'console_scripts': [
            'foo = foo.cli:main',
        ],
    },
)
```

```text tab="MANIFEST.in"
graft tests

global-exclude *.py[cod] __pycache__
```

=== "Hatch"
```toml tab="pyproject.toml"
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "foo"
description = "..."
readme = "README.md"
authors = [
  { name = "...", email = "..." },
]
classifiers = [
  "...",
]
keywords = [
  "...",
]
requires-python = ">=3.8"
dependencies = [
  "...",
]
dynamic = ["version"]

[project.urls]
Documentation = "..."
Source = "..."

[project.optional-dependencies]
feature = ["..."]

[project.scripts]
foo = "foo.cli:main"

[tool.hatch.version]
path = "src/foo/__about__.py"

[tool.hatch.build.targets.sdist]
include = [
  "/src",
  "/tests",
]
```

### 环境管理

=== "Tox"
调用方式：

```
tox
```

```ini tab="tox.ini"
[tox]
envlist =
    py{38,39}-{42,3.14}
    py{39,310}-{9000}-{foo,bar}

[testenv]
usedevelop = true
deps =
    coverage[toml]
    pytest
    pytest-cov
    foo: cryptography
commands =
    pytest --cov-report=term-missing --cov-config=pyproject.toml --cov=pkg --cov=tests {posargs}
setenv =
    3.14: PRODUCT_VERSION=3.14
    42: PRODUCT_VERSION=42
    9000: PRODUCT_VERSION=9000
    {foo,bar}: EXPERIMENTAL=true
```

=== "Hatch"
调用方式：

```
hatch run test
```

```toml config-example
[tool.hatch.envs.default]
dependencies = [
  "coverage[toml]",
  "pytest",
  "pytest-cov",
]

[tool.hatch.envs.default.scripts]
test = 'pytest --cov-report=term-missing --cov-config=pyproject.toml --cov=pkg --cov=tests'

[tool.hatch.envs.default.overrides]
matrix.version.env-vars = "PRODUCT_VERSION"
matrix.feature.env-vars = "EXPERIMENTAL=true"
matrix.feature.dependencies = [
  { value = "cryptography", if = ["foo"] },
]

[[tool.hatch.envs.default.matrix]]
python = ["3.8", "3.9"]
version = ["42", "3.14"]

[[tool.hatch.envs.default.matrix]]
python = ["3.9", "3.10"]
version = ["9000"]
feature = ["foo", "bar"]
```

## 快速命令行工具？

关于其比其他工具更快的说法，源于持续集成中始终会运行的[定时测试](https://github.com/pypa/hatch/actions/workflows/cli.yml)。

Hatch 的速度优势来自于延迟导入、手动延迟计算配合使用 [functools.cached_property](https://docs.python.org/3/library/functools.html#functools.cached_property)、以及像使用 `not not ...` 代替 `bool(...)` 这样的技巧。