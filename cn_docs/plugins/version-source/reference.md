# 版本源插件

-----

## 已知的第三方插件

- [hatch-vcs](https://github.com/ofek/hatch-vcs) - 使用您首选的版本控制系统（如 Git）
- [hatch-nodejs-version](https://github.com/agoose77/hatch-nodejs-version) - 使用 NodeJS `package.json` 文件中的 `version` 字段
- [hatch-regex-commit](https://github.com/frankie567/hatch-regex-commit) - 自动创建 Git 提交和标签，以实现版本增量
- [versioningit](https://github.com/jwodder/versioningit) - 根据 Git 或 Mercurial 标签确定版本，支持可定制的版本格式

::: hatchling.version.source.plugin.interface.VersionSourceInterface
    options:
      members:
      - PLUGIN_NAME
      - root
      - config
      - get_version_data
      - set_version
