# 测试构建插件

-----

在测试 [Hatchling 插件](../../plugins/about.md#hatchling) 时，通常需要生成一个项目，以模拟真实用户的构建行为。以下是一个最简的 [pytest](https://github.com/pytest-dev/pytest) fixture 示例：

```python
from pathlib import Path

import pytest


@pytest.fixture
def new_project(tmp_path):
    project_dir = tmp_path / 'my-app'
    project_dir.mkdir()

    project_file = project_dir / 'pyproject.toml'
    project_file.write_text(
        f"""\
[build-system]
requires = ["hatchling", "hatch-plugin-name @ {Path.cwd().as_uri()}"]
build-backend = "hatchling.build"

[project]
name = "my-app"
version = "0.1.0"
""",
        encoding='utf-8',
    )
    ...
```

上述方式的问题在于：第一次测试会使 `pip` 根据文件路径缓存该项目，此后即使代码更新，测试也不会重新使用新代码。

为了解决缓存问题，应在每次测试会话中将插件代码复制到一个新路径：

```python
import shutil
from pathlib import Path
from tempfile import TemporaryDirectory

import pytest


@pytest.fixture(scope='session')
def plugin_dir():
    with TemporaryDirectory() as d:
        directory = Path(d, 'plugin')
        shutil.copytree(
            Path.cwd(), directory, ignore=shutil.ignore_patterns('.git')
        )

        yield directory.resolve()


@pytest.fixture
def new_project(tmp_path, plugin_dir):
    project_dir = tmp_path / 'my-app'
    project_dir.mkdir()

    project_file = project_dir / 'pyproject.toml'
    project_file.write_text(
        f"""\
[build-system]
requires = ["hatchling", "hatch-plugin-name @ {plugin_dir.as_uri()}"]
build-backend = "hatchling.build"

[project]
name = "my-app"
version = "0.1.0"
""",
        encoding='utf-8',
    )
    ...
```

!!! note
    本示例出于性能考虑选择忽略 `.git` 文件夹。您可能还需忽略更多文件，或像 [此插件](https://github.com/hynek/hatch-fancy-pypi-readme/blob/main/tests/conftest.py) 一样，仅复制特定路径。

