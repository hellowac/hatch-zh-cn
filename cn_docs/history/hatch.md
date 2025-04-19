# Hatch 历史记录

-----

本文档记录了 Hatch 项目的所有重要变更。

## 未发布（Unreleased）

***变更：***

- 环境类型插件不再需要支持伪构建环境（pseudo-build environment），因为现在任意环境都可以用于构建。以下方法已被移除：`build_environment`、`build_environment_exists`、`run_builder`、`construct_build_command`

***新增：***

- `version` 和 `project metadata` 命令现在支持未使用 Hatchling 作为构建后端的项目
- `version` 命令新增 `--force` 选项，允许在明确指定版本号时执行降级操作
- 构建环境现在可配置，默认构建环境为 `hatch-build`
- 环境接口新增以下方法和属性，以更好支持远程构建场景：`project_root`、`sep`、`pathsep`、`fs_context`
- 将 `packaging` 的最低支持版本提升至 24.2

## [1.13.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.13.0) - 2024-10-13 ## {: #hatch-v1.13.0 }

***新增：***

- 支持管理 Python 3.13 发行版

## [1.12.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.12.0) - 2024-05-28 ## {: #hatch-v1.12.0 }

***变更：***

- `run` / `env run` 与 `test` 命令现在将包含变量选项视为交集（intersection）而非并集（union），从而更精准地定位目标环境

***新增：***

- 新增控制 Python 发行版来源的能力
- 将 Ruff 升级至 0.4.5
- 将 PyApp 升级至 0.22.0，用于构建二进制包

***修复：***

- `fmt` 命令不再隐藏其执行的命令内容
- 为网络请求添加默认超时设置，有助于安装 Python 发行版时提高稳定性
- 修复 `config show` 命令的语法高亮对比度问题

## [1.11.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.11.1) - 2024-05-23 ## {: #hatch-v1.11.1 }

***新增：***

- 发布官方 GitHub Action 用于安装 Hatch

***修复：***

- 修复 `terminal.styles.spinner` 配置项的问题
- 修复用于二进制分发的预构建包中的入口点配置

## [1.11.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.11.0) - 2024-05-14 ## {: #hatch-v1.11.0 }

***新增：***

- 将 PyApp 升级至 0.21.1，用于构建二进制包

***修复：***

- 在 Linux 上，安装 Python 发行版时将根据 CPU 架构选择最高兼容版本，而非假设使用较新硬件

## [1.10.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.10.0) - 2024-05-02 ## {: #hatch-v1.10.0 }

***变更：***

- `run` / `env run`、`fmt` 与 `shell` 命令现在仅在当前目录不在项目中时才切换到项目根目录
- `shell` 命令现在接受一个参数用于指定要进入的环境，该参数会覆盖标准的选择机制；用于控制 shell 行为的参数已转换为标志（flags）

***新增：***

- 新增 `test` 命令
- `run` 命令现在可执行内联定义依赖与 Python 版本约束的脚本
- `virtual` 环境类型现在支持使用 UV 替代 pip 和 virtualenv
- 新增 `self report` 命令，用于向 GitHub 提交预填充的错误报告
- 静态分析使用的保留环境现在完全可配置
- 环境接口新增以下方法，以完全控制生命周期管理过程中的输出信息：`app_status_creation`、`app_status_pre_installation`、`app_status_post_installation`、`app_status_project_installation`、`app_status_dependency_state_check`、`app_status_dependency_installation_check`、`app_status_dependency_synchronization`
- 增加对 Windows 32 位版本的二进制支持
- `index` 发布器现在会读取任何 `~/.pypirc` 文件中的配置
- 新项目的 URL 元数据中默认使用 Git 用户名作为用户名
- 新增 `HATCH_DEBUG` 环境变量，启用后在未处理异常时显示本地变量
- `env show` 命令在使用 `--json` 标志时，现在也会输出所有内部环境的信息
- 默认 CPython 发行版升级至 20240415
- 默认 PyPy 发行版升级至 7.3.15
- 将 Ruff 升级至 0.4.2
- 将 PyApp 升级至 0.19.0，用于构建二进制包
- 将 Hatchling 的最低支持版本提升至 1.24.2
- 将 virtualenv 的最低支持版本提升至 20.26.1

***修复：***

- 保持大小写不敏感文件系统上的数据路径一致性
- 当项目通过元数据钩子派生依赖时，默认会显示钩子执行状态以提升响应性
- 正确支持仅包含 `pyproject.toml` 文件但无 `[project]` 表的项目，例如应用程序
- 修复 `fmt` 命令在自动安装插件依赖时的问题
- 修复新项目中 `types` 环境模板的依赖继承逻辑
- 修复 Python 3.12+ 中解包 Python 发行版时可能出现的 tar 文件警告
- 默认禁用 `fmt` 命令中的 Ruff 规则 `E501`，因其与格式化器存在冲突
- 修复首次构建时，目标构建环境的创建状态指示符未显示彩色输出的问题
- 设置 `packaging` 依赖版本为 `>=23.2`，以避免其 URL 验证逻辑与上下文格式冲突
- 修复出现未处理异常时返回的退出码
- 在解析构建环境中的元数据载荷时不再同时捕获 stdout 和 stderr 流
- 修复新项目生成的 `README.md` 模板中可能导致 Markdown lint 工具报错的问题

## [1.9.7](https://github.com/pypa/hatch/releases/tag/hatch-v1.9.7) - 2024-04-24 ## {: #hatch-v1.9.7 }

***Fixed:***

- Limit the maximum version of virtualenv due to a backward incompatible change
- Upgrade PyApp to 0.12.0 for binary builds

## [1.9.4](https://github.com/pypa/hatch/releases/tag/hatch-v1.9.4) - 2024-03-12 ## {: #hatch-v1.9.4 }

***Fixed:***

- Limit the maximum version of Hatchling in anticipation of backward incompatible changes

## [1.9.3](https://github.com/pypa/hatch/releases/tag/hatch-v1.9.3) - 2024-01-25 ## {: #hatch-v1.9.3 }

***Fixed:***

- Fix loading of local plugins to account for newly released versions of a dependency

## [1.9.2](https://github.com/pypa/hatch/releases/tag/hatch-v1.9.2) - 2024-01-21 ## {: #hatch-v1.9.2 }

***Fixed:***

- Fix the default token variable name for publishing to PyPI

## [1.9.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.9.1) - 2023-12-25 ## {: #hatch-v1.9.1 }

***Fixed:***

- Ensure that the `dependency_hash` method of the `environment` interface is called after `sync_dependencies` for cases where the hash is only known at that point, such as for dependency lockers
- Only acknowledge the `HATCH_PYTHON_VARIANT_*` environment variables for Python resolution for supported platforms and architectures
- Fix Python resolution when there are metadata hooks with unsatisfied dependencies

## [1.9.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.9.0) - 2023-12-19 ## {: #hatch-v1.9.0 }

***Changed:***

- Environments prefixed by `hatch-` are now considered internal and used for special purposes such as configuration for static analysis

***Added:***

- Enable docstring formatting by default for static analysis
- Allow for overriding config of internal environments
- Concretely state the expected API contract for the environment interface methods `find` and `check_compatibility`
- Upgrade Ruff to 0.1.8
- Bump the minimum supported version of Hatchling to 1.21.0

***Fixed:***

- Ignore a project's Python requirement for environments where the project is not installed
- When not persisting config for static analysis, properly manage internal settings when Ruff's top level table already exists
- Ignore compatibility checks when environments have already been created, significantly improving performance of environment usage
- Properly allow overriding of the `path` option for the `virtual` environment type
- Fix nushell activation on non-Windows systems

## [1.8.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.8.1) - 2023-12-14 ## {: #hatch-v1.8.1 }

***Fixed:***

- Fix regression in calling subprocesses with updated PATH
- Fix automatic installation of environment plugins when running as a standalone binary
- Change default location of Python installations

## [1.8.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.8.0) - 2023-12-11 ## {: #hatch-v1.8.0 }

***Changed:***

- Drop support for Python 3.7
- The `get_build_process` method of the `environment` interface has been removed; plugins should use the new `run_builder` method instead
- Remove `pyperclip` dependency and the `--copy` flag of the `config find` command
- When running the `build` command all output from builders is now displayed as-is in real time without the stripping of ANSI codes
- Version information (for Hatch itself) is now derived from Git

***Added:***

- Support Python 3.12
- Add installers and standalone binaries
- Add the ability to manage Python installations
- Add `fmt` command
- The `virtual` environment type can now automatically download requested versions of Python that are not installed
- Add `dependency_hash` method to the `environment` interface
- The state of installed dependencies for environments is saved as metadata so if dependency definitions have not changed then no checking is performed, which can be computationally expensive
- The `build` command now supports backends other than Hatchling
- Allow the use of `features` for environments when `skip-install` is enabled
- The default is now `__token__` when prompting for a username for the `publish` command
- Add a new `run_builder` method to the `environment` interface
- Bump the minimum supported version of Hatchling to 1.19.0
- Bump the minimum supported version of `click` to 8.0.6

***Fixed:***

- Fix nushell activation
- Better handling of flat storage directory hierarchies for the `virtual` environment type
- Display useful information when running the `version` command outside of a project rather than erroring
- Fix the `project metadata` command by only capturing stdout from the backend
- Properly support Google Artifact Registry
- Fix parsing dependencies for environments when warnings are emitted

## [1.7.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.7.0) - 2023-04-03 ## {: #hatch-v1.7.0 }

***Changed:***

- The `src-layout` project template option is now enabled by default
- Non-critical output now goes to stderr

***Added:***

- Add `tool.hatch.env.requires` configuration to automatically install dependencies for environment and environment collector plugins
- Add `custom` environment collector
- Improve syncing of dependencies provided through Git direct references
- Add `isolated_data_directory` attribute to the environment interface
- Increase the timeout for and add retries to the `index` publisher
- Expand home and environment variables in configured cache and data directories
- Improve readability of exceptions
- Update project templates
- Bump the minimum supported version of Hatchling to 1.14.0

***Fixed:***

- Fix displaying the version with the `version` command when the version is static and build dependencies are unmet
- Fix build environments for the `virtual` environment type when storing within a relative path
- Work around System Integrity Protection on macOS when running commands
- Allow setuptools metadata migration for projects without `setup.py` if `setup.cfg` is present
- Handle additional edge cases for setuptools metadata migration
- Support boolean values for the `config set` command

## [1.6.3](https://github.com/pypa/hatch/releases/tag/hatch-v1.6.3) - 2022-10-24 ## {: #hatch-v1.6.3 }

***Fixed:***

- Fix `version` command when the version is dynamic and build dependencies are unmet

## [1.6.2](https://github.com/pypa/hatch/releases/tag/hatch-v1.6.2) - 2022-10-20 ## {: #hatch-v1.6.2 }

***Fixed:***

- Fix getting dynamic metadata from hooks for environments when dependencies are not dynamic

## [1.6.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.6.1) - 2022-10-16 ## {: #hatch-v1.6.1 }

***Fixed:***

- Computing the path to the user's home directory now gracefully falls back to `~` when it cannot be determined

## [1.6.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.6.0) - 2022-10-08 ## {: #hatch-v1.6.0 }

***Changed:***

- The `run_shell_command` environment interface method now accepts arbitrary `subprocess.Popen` keyword arguments. This is not strictly breaking, but will be utilized in upcoming features.
- The internal directory structure for storing `virtual` environments is now more nested. This is not breaking, but any local environments will be created anew.

***Added:***

- Add `project` command group to view details about the project like PEP 621 metadata
- Better support for auto-detection of environments by tools like Visual Studio Code now that the storage directory of `virtual` environments will be flat if Hatch's configured `virtual` environment directory resides somewhere within the project root or if it is set to a `.virtualenvs` directory within the user's home directory
- Build environments for the `virtual` environment type are now cached for improved performance
- Add `build_environment_exists` method to the environment interface for implementations that cache the build environment
- Add `path` option to the `virtual` environment type
- Add `--initialize-auth` flag to the `index` publisher to allow for the saving of authentication information before publishing
- Support Bash on Windows for the `shell` command
- The `setuptools` migration script no longer modifies the formatting of existing `pyproject.toml` configuration
- Bump the minimum supported version of Hatchling to 1.11.0

***Fixed:***

- Environments now respect dynamically defined project dependencies
- The `dep hash` and all `dep show` commands now respect dynamically defined project dependencies
- The `env show`, `dep hash`, and all `dep show` commands now honor context formatting
- Fix matrix variable inclusion filtering of the `run` and `env run` commands when there are multiple possible variables
- Build environment compatibility is now checked before use
- Decreasing verbosity now has no affect on output that should always be displayed
- Handle more edge cases in the `setuptools` migration script
- Environments now respect user defined environment variables for context formatting
- Update the scripts in the generated test environment template for new projects to reflect the documentation
- Allow `extra-dependencies` in environment overrides
- Depend on `packaging` explicitly rather than relying on it being a transitive dependency of Hatchling

## [1.5.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.5.0) - 2022-08-28 ## {: #hatch-v1.5.0 }

***Added:***

- The `index` publisher now recognizes repository-specific options
- Add the `--ignore-compat` flag to the `env run` command
- Setting the `HATCH_PYTHON` environment variable to `self` will now force the use of the Python executable Hatch is running on for `virtual` environment creation

***Fixed:***

- Fix the `--force-continue` flag of the `env run` command
- Handle more edge cases in the `setuptools` migration script

## [1.4.2](https://github.com/pypa/hatch/releases/tag/hatch-v1.4.2) - 2022-08-16 ## {: #hatch-v1.4.2 }

***Fixed:***

- Fix check for updating static versions with the `version` command when metadata hooks are in use

## [1.4.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.4.1) - 2022-08-13 ## {: #hatch-v1.4.1 }

***Fixed:***

- Fix non-detached inheritance disabling for environments

## [1.4.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.4.0) - 2022-08-06 ## {: #hatch-v1.4.0 }

***Added:***

- The default Python for `virtual` environments now checks PATH before using the one Hatch is running on
- Values for environment `env-vars` now support context formatting
- Add `name` override for environments to allow for regular expression matching
- The `index` publisher now better supports non-PyPI indices
- Add certificate options to the `index` publisher
- Display waiting text when checking dependencies and removing environments
- Display help text the first time the `shell` command is executed
- Update project templates with Python 3.11 and the latest versions of various GitHub Actions
- Add support for Almquist (`ash`) shells
- Add `hyperlink` as a dependency for better handling of package index URLs
- Bump the minimum supported version of `virtualenv` to 20.16.2
- Bump the minimum supported version of `tomlkit` to 0.11.1

***Fixed:***

- Acknowledge `extra-dependencies` for the `env show` command
- Fix locating executables within virtual environments on Debian
- Fix managing the terminal size inside the `shell` command
- Fix default code coverage file omission for the `src-layout` project template option

## [1.3.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.3.1) - 2022-07-11 ## {: #hatch-v1.3.1 }

***Fixed:***

- Support `-h`/`--help` flag for the `run` command

## [1.3.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.3.0) - 2022-07-10 ## {: #hatch-v1.3.0 }

***Changed:***

- Rename the default publishing plugin from `pypi` to the more generic `index`

***Added:***

- Support the absence of `pyproject.toml` files, as is the case for apps and non-Python projects
- Hide scripts that start with an underscore for the `env show` command by default
- Ignoring the exit codes of commands by prefixing with hyphens now works with entire named scripts
- Add a way to require confirmation for publishing
- Add `--force-continue` flag to the `env run` command
- Make tracebacks colorful and less verbose
- When shell configuration has not been defined, attempt to use the current shell based on parent processes before resorting to the defaults
- The shell name `pwsh` is now an alias for `powershell`
- Remove `atomicwrites` dependency
- Relax constraint on `userpath` dependency
- Bump the minimum supported version of Hatchling to 1.4.1

***Fixed:***

- Keep environments in sync with the dependencies of the selected features
- Use `utf-8` for all files generated for new projects
- Escape special characters Git may return in the user name when writing generated files for new projects
- Normalize the package name to lowercase in `setuptools` migration script
- Fix parsing of source distributions during publishing

## [1.2.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.2.1) - 2022-05-30 ## {: #hatch-v1.2.1 }

***Fixed:***

- Fix handling of top level `data_files` in `setuptools` migration script

## [1.2.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.2.0) - 2022-05-22 ## {: #hatch-v1.2.0 }

***Changed:***

- The `enter_shell` environment plugin method now accepts an additional `args` parameter

***Added:***

- Allow context string formatting for environment dependencies
- Add environment context string formatting fields `env_name`, `env_type`, `matrix`, `verbosity`, and `args`
- Support overriding the default arguments used to spawn shells on non-Windows systems
- Bump the minimum supported version of Hatchling to 1.3.0

***Fixed:***

- Improve `setuptools` migration script

## [1.1.2](https://github.com/pypa/hatch/releases/tag/hatch-v1.1.2) - 2022-05-20 ## {: #hatch-v1.1.2 }

***Fixed:***

- Bump the minimum supported version of Hatchling to 1.2.0
- Update project metadata to reflect support for Python 3.11

## [1.1.1](https://github.com/pypa/hatch/releases/tag/hatch-v1.1.1) - 2022-05-12 ## {: #hatch-v1.1.1 }

***Fixed:***

- Fix `setuptools` migration script for non-Windows systems

## [1.1.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.1.0) - 2022-05-12 ## {: #hatch-v1.1.0 }

***Changed:***

- In order to simplify the implementation of command execution for environment plugins, the `run_shell_commands` method has been replaced by the singular `run_shell_command`. A new `command_context` method has been added to more easily satisfy complex use cases.
- The `finalize_command` environment plugin method has been removed in favor of the newly introduced context formatting functionality.

***Added:***

- Add context formatting functionality i.e. the ability to insert values into configuration like environment variables and command line arguments
- Any verbosity for command execution will now always display headers, even for single environments
- Every executed command is now displayed when running multiple commands or when verbosity is enabled
- Similar to `make`, ignore the exit code of executed commands that start with `-` (a hyphen)
- Add ability for the `--init` flag of the `new` command to automatically migrate `setuptools` configuration
- Update project metadata to reflect the adoption by PyPA and production stability

## [1.0.0](https://github.com/pypa/hatch/releases/tag/hatch-v1.0.0) - 2022-04-28 ## {: #hatch-v1.0.0 }

This is the first stable release of Hatch v1, a complete rewrite. Enjoy!
