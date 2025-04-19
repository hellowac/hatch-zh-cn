# 元数据钩子插件（Metadata hook plugins）

-----

元数据钩子允许在加载项目元数据后进行修改。

## 知名第三方插件（Known third-party）

- [hatch-docstring-description](https://github.com/flying-sheep/hatch-docstring-description) - 使用 docstring 设置项目描述
- [hatch-fancy-pypi-readme](https://github.com/hynek/hatch-fancy-pypi-readme) - 动态构建 README
- [hatch-nodejs-version](https://github.com/agoose77/hatch-nodejs-version) - 使用 NodeJS `package.json` 文件中的字段
- [hatch-odoo](https://github.com/acsone/hatch-odoo) - 基于 Odoo 插件的清单来确定依赖项
- [hatch-requirements-txt](https://github.com/repo-helper/hatch-requirements-txt) - 从 `requirements.txt` 文件读取项目依赖
- [UniDep](https://github.com/basnijholt/unidep) - 使用单个 `requirements.yaml` 文件实现统一的 `pip` 和 `conda` 依赖管理

::: hatchling.metadata.plugin.interface.MetadataHookInterface
    options:
      members:
      - PLUGIN_NAME
      - root
      - config
      - update
      - get_known_classifiers
