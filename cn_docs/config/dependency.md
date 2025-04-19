# 依赖配置

-----

[项目依赖](metadata.md#dependencies)使用 [PEP 508][] 字符串来定义，并可使用可选的 [PEP 440 版本说明符][]。

## 版本说明符

版本说明符由一系列版本子句组成，这些子句之间用逗号分隔。例如：

```toml tab="pyproject.toml"
[project]
...
dependencies = [
  "cryptography",
  "click>=7, <9, != 8.0.0",
  "python-dateutil==2.8.*",
  "numpy~=1.21.4",
]
```

逗号相当于逻辑 `AND` 运算符：候选版本必须满足所有给定的版本子句，才能整体符合说明符。

### 操作符

| 操作符 | 功能 |
| :---: | --- |
| `~=` | [兼容发布](#兼容发布) |
| `==` | [版本匹配](#版本匹配) |
| `!=` | [版本排除](#版本排除) |
| `<=`, `>=` | [包含的有序比较](#有序比较) |
| `<`, `>` | [排除的有序比较](#有序比较) |
| `===` | [任意相等](#任意相等) |

### 版本匹配

版本匹配子句包括版本匹配操作符 `==` 和版本标识符。

默认情况下，版本匹配操作符基于严格的相等比较：指定的版本必须与请求的版本完全相同。

| 子句 | 允许的版本 |
| --- | --- |
| `==1` | `1.0.0` |
| `==1.2` | `1.2.0` |

可以通过在版本标识符后附加 `.*` 来请求前缀匹配，这意味着在确定版本是否匹配时，额外的尾部部分将被忽略。

| 子句 | 允许的版本 |
| --- | --- |
| `==1.*` | `>=1.0.0, <2.0.0` |
| `==1.2.*` | `>=1.2.0, <1.3.0` |

### 兼容发布

兼容发布子句由兼容发布操作符 `~=` 和版本标识符组成。它匹配任何预期与指定版本兼容的候选版本。

对于给定的版本标识符 `V.N`，兼容发布子句大致相当于以下一对比较子句：

```
>= V.N, == V.*
```

此操作符不能与单段版本号（如 `~=1`）一起使用。

| 子句 | 允许的版本 |
| --- | --- |
| `~=1.2` | `>=1.2.0, <2.0.0` |
| `~=1.2.3` | `>=1.2.3, <1.3.0` |

### 版本排除

版本排除子句包括版本排除操作符 `!=` 和版本标识符。

允许的版本标识符和比较语义与 [版本匹配](#版本匹配) 操作符相同，不同之处在于任何匹配的意义是相反的。

### 有序比较

包含比较允许版本标识符部分，而排除比较则不允许。例如，`>=1.2` 允许版本 `1.2.0`，而 `>1.2` 则不允许。

与包含有序比较 `<=` 和 `>=` 不同，排除的有序比较 `<` 和 `>` 特别排除预发布、后发布和本地版本。

### 任意相等

尽管强烈不推荐，任意相等比较允许简单的字符串匹配，而不考虑任何版本语义，例如 `===foobar`。

## 环境标记

[环境标记](https://peps.python.org/pep-0508/#environment-markers)允许仅在满足某些条件时安装依赖项。

例如，如果您需要在给定 Python 主版本下安装最新版本的 `cryptography`，可以定义如下：

```
cryptography==3.3.2; python_version < "3"
cryptography>=35.0; python_version > "3"
```

或者，如果您只在 Python 3 上并且运行在 Windows 上时需要它，您可以这样做：

```
cryptography; python_version ~= "3.0" and platform_system == "Windows"
```

可用的环境标记如下：

| 标记 | Python 等效 | 示例 |
| --- | --- | --- |
| `os_name` | `#!python import os`<br>`os.name` | <ul><li>posix</li><li>java</li></ul> |
| `sys_platform` | `#!python import sys`<br>`sys.platform` | <ul><li>linux</li><li>win32</li><li>darwin</li></ul> |
| `platform_machine` | `#!python import platform`<br>`platform.machine()` | <ul><li>x86_64</li></ul> |
| `platform_python_implementation` | `#!python import platform`<br>`platform.python_implementation()` | <ul><li>CPython</li><li>Jython</li></ul> |
| `platform_release` | `#!python import platform`<br>`platform.release()` | <ul><li>1.8.0_51</li><li>3.14.1-x86_64-linode39</li></ul> |
| `platform_system` | `#!python import platform`<br>`platform.system()` | <ul><li>Linux</li><li>Windows</li><li>Darwin</li></ul> |
| `platform_version` | `#!python import platform`<br>`platform.version()` | <ul><li>10.0.19041</li><li>\#1 SMP Fri Apr 2 22:23:49 UTC 2021</li></ul> |
| `python_version` | `#!python import platform`<br>`'.'.join(platform.python_version_tuple()[:2])` | <ul><li>2.7</li><li>3.10</li></ul> |
| `python_full_version` | `#!python import platform`<br>`platform.python_version()` | <ul><li>2.7.18</li><li>3.11.0b1</li></ul> |
| `implementation_name` | `#!python import sys`<br>`sys.implementation.name` | <ul><li>cpython</li></ul> |
| `implementation_version` | 见 [这里](https://peps.python.org/pep-0508/#environment-markers) | <ul><li>2.7.18</li><li>3.11.0b1</li></ul> |

## 特性

您可以使用 [extras](https://peps.python.org/pep-0508/#extras) 语法选择安装一组 [可选依赖](metadata.md#optional) 依赖项。例如，如果一个名为 `foo` 的依赖定义了以下内容：

```toml tab="pyproject.toml"
[project.optional-dependencies]
crypto = [
  "PyJWT",
  "cryptography",
]
fastjson = [
  "orjson",
]
cli = [
  "prompt-toolkit",
  "colorama; platform_system == 'Windows'",
]
```

您可以通过如下方式选择 `cli` 和 `crypto` 特性：

```
foo[cli,crypto]==1.*
```

注意，特性应紧跟在包名之后，位于任何 [版本说明符](#版本说明符) 之前。

### 自我引用

特性组可以自我引用地扩展其他组。例如，对于一个名为 `awesome-project` 的项目，下面 `pyproject.toml` 文件中的 `dev` 特性组将选择 `crypto` 特性组中的所有内容，以及 `black`：

```toml tab="pyproject.toml"
[project]
name = "awesome-project"

[project.optional-dependencies]
crypto = [
  "PyJWT",
  "cryptography",
]
dev = [
  "awesome-project[crypto]",
  "black",
]
```

## 直接引用

除了使用正常的 [版本说明符](#版本说明符) 并从类似 PyPI 的索引获取包外，您还可以通过显式的 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier#Syntax) 定义确切的源，使用 [直接引用](https://peps.python.org/pep-0440/#direct-references)。

直接引用通常不用于已发布项目的依赖项，而是用于定义 [环境的依赖项](environment/overview.md#dependencies)。

所有直接引用类型都以包名为前缀，如下所示：

```
<NAME> @ <REFERENCE>
```

### 版本控制系统

只要相关的可执行文件可用并且在您的 `PATH` 中，就支持多种版本控制系统（VCS）。

VCS 直接引用使用以下格式之一：

```
<NAME> @ <SCHEME>://<PATH>
<NAME> @ <SCHEME>://<PATH>@<REVISION>
```

您还可以附加 `#subdirectory=<PATH>` 部分，以指定当 Python 包不在根目录时相对路径，例如 `#subdirectory=lib/foo`。

有关更多信息，请参考 [此处](https://pip.pypa.io/en/stable/topics/vcs-support/)。

#### 支持的 VCS

=== "Git"
    | 可执行文件 | 协议 | 修订 | 示例 |
    | --- | --- | --- | --- |
    | `git` | <ul><li><code>git+file</code></li><li><code>git+https</code></li><li><code>git+ssh</code></li><li><code>git+http</code> :warning:</li><li><code>git+git</code> :warning:</li><li><code>git</code> :warning:</li></ul> | <ul><li>提交哈希</li><li>标签名称</li><li>分支名称</li></ul> | `proj @ git+https://github.com/org/proj.git@v1` |

=== "Mercurial"
    | 可执行文件 | 协议 | 修订 | 示例 |
    | --- | --- | --- | --- |
    | `hg` | <ul><li><code>hg+file</code></li><li><code>hg+https</code></li><li><code>hg+ssh</code></li><li><code>hg+http</code> :warning:</li><li><code>hg+static-http</code> :warning:</li></ul> | <ul><li>修订哈希</li><li>修订编号</li><li>标签名称</li><li>分支名称</li></ul> | `proj @ hg+file:///path/to/proj@v1` |

=== "Subversion"
    | 可执行文件 | 协议 | 修订 | 示例 |
    | --- | --- | --- | --- |
    | `svn` | <ul><li><code>svn+https</code></li><li><code>svn+ssh</code></li><li><code>svn+http</code> :warning:</li><li><code>svn+svn</code> :warning:</li><li><code>svn</code> :warning:</li></ul> | <ul><li>修订编号</li></ul> | `proj @ svn+file:///path/to/proj` |

=== "Bazaar"
    | 可执行文件 | 协议 | 修订 | 示例 |
    | --- | --- | --- | --- |
    | `bzr` | <ul><li><code>bzr+https</code></li><li><code>bzr+ssh</code></li><li><code>bzr+sftp</code></li><li><code>bzr+lp</code></li><li><code>bzr+http</code> :warning:</li><li><code>bzr+ftp</code> :warning:</li></ul> | <ul><li>修订编号</li><li>标签名称</li></ul> | `proj @ bzr+lp:proj@v1` |

### 本地

您可以使用 `file` 协议安装本地包，格式如下：

```
<NAME> @ file://<HOST>/<PATH>
```

在 Windows 系统中，`<HOST>` 仅用于网络共享。如果省略，则假定为 `localhost`，且第三个斜杠仍然存在。

`<PATH>` 可以指向源归档、轮子文件或包含 Python 包的目录。

| 类型 | Unix | Windows |
| --- | --- | --- |
| 源归档 | `proj @ file:///path/to/pkg.tar.gz` | `proj @ file:///c:/path/to/pkg.tar.gz` |
| 轮子文件 | `proj @ file:///path/to/pkg.whl` | `proj @ file:///c:/path/to/pkg.whl` |
| 目录 | `proj @ file:///path/to/pkg` | `proj @ file:///c:/path/to/pkg` |

!!! 提示
    您还可以通过使用 [上下文格式化](context.md#paths) 指定相对于项目根目录的路径：

    ```
    <NAME> @ {root:uri}/pkg_inside_project
    <NAME> @ {root:parent:uri}/pkg_alongside_project
    ```

### 远程

您可以通过简单地引用 URL 来安装源归档和轮子文件：

```
black @ https://github.com/psf/black/archive/refs/tags/21.10b0.zip
pytorch @ https://download.pytorch.org/whl/cu102/torch-1.10.0%2Bcu102-cp39-cp39-linux_x86_64.whl
```

可以通过附加 `#<HASH_ALGORITHM>=<EXPECTED_HASH>` 组件来指定期望的哈希值：

```
requests @ https://github.com/psf/requests/archive/refs/tags/v2.26.0.zip#sha256=eb729a757f01c10546ebd179ae2aec852dd0d7f8ada2328ccf4558909d859985
```

如果哈希与期望的哈希不同，安装将失败。

建议使用 [PEP 503](https://peps.python.org/pep-0503/) 兼容的索引，或通过 HTTPS 访问 URL 以确保传输安全。