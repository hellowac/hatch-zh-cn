# 构建

-----

## 配置

构建通过 `tool.hatch.build` 表进行 [配置](config/build.md)。每个 [目标](config/build.md#build-targets) 都在 `tool.hatch.build.targets` 的子节中定义，例如：

```toml config-example
[tool.hatch.build.targets.sdist]
exclude = [
  "/.github",
  "/docs",
]

[tool.hatch.build.targets.wheel]
packages = ["src/foo"]
```

## 执行构建

运行 [`build`](cli/reference.md#hatch-build) 命令且不带任何参数时，将构建 [sdist](plugins/builder/sdist.md) 和 [wheel](plugins/builder/wheel.md) 两个目标：

```console
$ hatch build
[sdist]
dist/hatch_demo-1rc0.tar.gz

[wheel]
dist/hatch_demo-1rc0-py3-none-any.whl
```

若仅希望构建某些特定目标，可以使用 `-t`/`--target` 选项：

```console
$ hatch build -t wheel
[wheel]
dist/hatch_demo-1rc0-py3-none-any.whl
```

若该目标支持多个 [版本](config/build.md#versions)，可以在目标后添加冒号并用逗号分隔多个版本，以指定具体要构建的版本：

```console
$ hatch -v build -t wheel:standard
[wheel]
Building `wheel` version `standard`
dist/hatch_demo-1rc0-py3-none-any.whl
```

## 打包生态

Hatch [符合](config/build.md#build-system) 现代 Python 打包规范，因此你的项目在使用 Hatch 作为构建后端的前提下，也可以被其他工具使用。

例如，你可以使用 [tox](https://github.com/tox-dev/tox) 来替代 Hatch 的 [环境管理](environment.md)，或使用 [cibuildwheel](https://github.com/pypa/cibuildwheel) 来打包所有平台的构建产物，而这些工具都可以在不需要额外配置的前提下，透明地调用 Hatch 完成构建。
