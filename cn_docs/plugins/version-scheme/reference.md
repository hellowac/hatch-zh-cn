# 版本方案插件

-----

## 已知的第三方插件

- [hatch-semver](https://github.com/Nagidal/hatch-semver) - 使用 [语义化版本控制](https://semver.org)

::: hatchling.version.scheme.plugin.interface.VersionSchemeInterface
    options:
      members:
      - PLUGIN_NAME
      - root
      - config
      - validate_bump
      - update
