# 环境插件（Environment plugins）

-----

请参阅 [环境配置文档](../../config/environment/overview.md)。

## 知名第三方插件（Known third-party）

- [hatch-conda](https://github.com/OldGrumpyViking/hatch-conda) - 基于 Conda/Mamba 的环境
- [hatch-containers](https://github.com/ofek/hatch-containers) - 在容器中运行的环境
- [hatch-pip-compile](https://github.com/juftin/hatch-pip-compile) - 使用 [pip-compile](https://github.com/jazzband/pip-tools) 管理项目依赖和锁文件
- [hatch-pip-deepfreeze](https://github.com/sbidoul/hatch-pip-deepfreeze) - 使用 [pip-deepfreeze](https://github.com/sbidoul/pip-deepfreeze) 进行依赖锁定的 [虚拟环境](virtual.md)

## 安装（Installation）

任何非内置的环境类型必须手动安装，并与 Hatch 一起使用，或者在 `tool.hatch.env.requires` 数组中列出，以便进行自动管理：

```toml config-example
[tool.hatch.env]
requires = [
  "...",
]
```

## 生命周期（Life cycle）

每当使用环境时，将执行以下逻辑：

::: hatch.project.core.Project.prepare_environment
    options:
      show_root_heading: false
      show_root_toc_entry: false

## 构建环境（Build environments）

所有环境类型都应 [提供支持](#hatch.env.plugin.interface.EnvironmentInterface.fs_context)，以便在本地文件系统和环境之间进行同步存储。该功能在以下场景中使用：

- [`build`](../../cli/reference.md#hatch-build) 命令
- 读取依赖项的命令，如 [`dep hash`](../../cli/reference.md#hatch-dep-hash)，如果存在任何 [项目依赖](../../config/metadata.md#dependencies) 是 [动态设置的](../../config/metadata.md#dynamic)

Whenever an environment is used, the following logic is performed:

::: hatch.project.core.Project.prepare_environment
    options:
      show_root_heading: false
      show_root_toc_entry: false

## Build environments

All environment types should [offer support](#hatch.env.plugin.interface.EnvironmentInterface.fs_context) for synchronized storage between the local file system and the environment. This functionality is used in the following scenarios:

- the [`build`](../../cli/reference.md#hatch-build) command
- commands that read dependencies, like [`dep hash`](../../cli/reference.md#hatch-dep-hash), if any [project dependencies](../../config/metadata.md#dependencies) are [set dynamically](../../config/metadata.md#dynamic)

::: hatch.env.plugin.interface.EnvironmentInterface
    options:
      members:
      - PLUGIN_NAME
      - find
      - create
      - remove
      - exists
      - install_project
      - install_project_dev_mode
      - dependencies_in_sync
      - sync_dependencies
      - dependency_hash
      - project_root
      - sep
      - pathsep
      - fs_context
      - activate
      - deactivate
      - app_status_creation
      - app_status_pre_installation
      - app_status_post_installation
      - app_status_project_installation
      - app_status_dependency_state_check
      - app_status_dependency_installation_check
      - app_status_dependency_synchronization
      - app
      - root
      - name
      - data_directory
      - isolated_data_directory
      - config
      - platform
      - environment_dependencies
      - dependencies
      - env_vars
      - env_include
      - env_exclude
      - platforms
      - skip_install
      - dev_mode
      - description
      - command_context
      - enter_shell
      - run_shell_command
      - resolve_commands
      - get_env_vars
      - apply_features
      - construct_pip_install_command
      - join_command_args
      - check_compatibility
      - get_option_types
      - get_env_var_option
      - get_context
