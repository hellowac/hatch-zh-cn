# 构建器插件

-----

请参阅[构建配置](../../config/build.md)的文档。

## Known third-party

- [hatch-aws](https://github.com/aka-raccoon/hatch-aws) - 用于使用 SAM 构建 AWS Lambda 函数
- [hatch-zipped-directory](https://github.com/dairiki/hatch-zipped-directory) - 用于构建 ZIP 档案，以便安装到各种外部软件包安装系统中

::: hatchling.builders.plugin.interface.BuilderInterface
    options:
      members:
      - PLUGIN_NAME
      - app
      - root
      - build_config
      - target_config
      - config
      - get_config_class
      - get_version_api
      - get_default_versions
      - clean
      - recurse_included_files
      - get_default_build_data
