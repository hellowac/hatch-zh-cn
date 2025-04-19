# 发布

-----

在你的项目完成 [构建](build.md) 之后，可以使用 [`publish`](cli/reference.md#hatch-publish) 命令将其分发。

`-p`/`--publisher` 选项用于控制所使用的发布器，默认值为 [index](plugins/publisher/package-index.md)。

## 制品选择

默认情况下，将使用项目根目录下的 `dist` 目录中的文件：

```console
$ hatch publish
dist/hatch_demo-1rc0-py3-none-any.whl ... success
dist/hatch_demo-1rc0.tar.gz ... success

[hatch-demo]
https://pypi.org/project/hatch-demo/1rc0/
```

你也可以直接传递具体路径作为参数：

```
hatch publish /path/to/artifacts foo-1.tar.gz
```

只有以 `.whl` 或 `.tar.gz` 结尾的文件会被发布。

## 更多资源

请参考发布插件的 [参考文档](plugins/publisher/package-index.md)，以获取配置选项。

你也可以查阅关于 [认证](how-to/publish/auth.md) 的使用方法，以及如何选择目标 [仓库](how-to/publish/repo.md)。

`publish` 命令本身是一个内建插件。如果你计划编写自己的插件，请阅读 [publisher 插件 API](plugins/publisher/reference.md)。
