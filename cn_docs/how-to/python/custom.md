# 如何使用自定义 Python 发行版

-----

Hatch 内建的 [Python 管理功能](../../tutorials/python/manage.md) 完全支持使用自定义发行版。

## 配置方式

配置自定义 Python 发行版完全通过三个环境变量进行，并且对于每个发行版，三个变量都必须定义。在以下各小节中，`<NAME>` 是发行版名称的全大写形式，且将其中的点（`.`）替换为下划线。例如：`pypy3.10` 应写作 `PYPY3_10`。

### 源地址（Source）

变量 `HATCH_PYTHON_CUSTOM_SOURCE_<NAME>` 用于指定发行版归档文件的 URL。值必须以归档文件的实际扩展名结尾，用于判定解压方法。

支持的扩展名如下：

| 扩展名 | 描述 |
| --- | --- |
| <ul><li><code>.tar.bz2</code></li><li><code>.bz2</code></li></ul> | 使用 [bzip2 压缩](https://en.wikipedia.org/wiki/Bzip2) 的 [tar 文件](https://en.wikipedia.org/wiki/Tar_(computing)) |
| <ul><li><code>.tar.gz</code></li><li><code>.tgz</code></li></ul> | 使用 [gzip 压缩](https://en.wikipedia.org/wiki/Gzip) 的 [tar 文件](https://en.wikipedia.org/wiki/Tar_(computing)) |
| <ul><li><code>.tar.zst</code></li><li><code>.tar.zstd</code></li></ul> | 使用 [Zstandard 压缩](https://en.wikipedia.org/wiki/Zstd) 的 [tar 文件](https://en.wikipedia.org/wiki/Tar_(computing)) |
| <ul><li><code>.zip</code></li></ul> | 使用 [DEFLATE 压缩](https://en.wikipedia.org/wiki/Deflate) 的 [ZIP 文件](https://en.wikipedia.org/wiki/ZIP_(file_format)) |

### Python 路径

变量 `HATCH_PYTHON_CUSTOM_PATH_<NAME>` 指定归档文件中 Python 解释器的路径。该路径相对于归档根目录，必须为 Unix 风格路径，即使在 Windows 上也是如此。

### 版本号

变量 `HATCH_PYTHON_CUSTOM_VERSION_<NAME>` 表示发行版的版本号。该值用于判断是否需要更新，并显示于 [`python show`](../../cli/reference.md#hatch-python-show) 命令的输出中。
