# 安装

-----

## GitHub Actions

```yaml
- name: Install Hatch
  uses: pypa/hatch@install
```

更多信息请参考 [官方操作](https://github.com/pypa/hatch/tree/install)。

## 安装程序

=== "macOS"
    === "图形界面安装器"
        1. 在浏览器中下载 `.pkg` 文件：[hatch-universal.pkg](https://github.com/pypa/hatch/releases/latest/download/hatch-universal.pkg)
        2. 运行你下载的文件并按照屏幕提示操作。
        3. 重启终端。
        4. 使用以下命令确认 shell 能够在 `PATH` 中找到并运行 `hatch` 命令：

            ```
            $ hatch --version
            <HATCH_LATEST_VERSION>
            ```
    === "命令行安装器"
        1. 使用 `curl` 命令下载文件。`-o` 参数指定保存的文件名。在此示例中，文件将被保存为当前目录下的 `hatch-universal.pkg`。

            ```
            curl -Lo hatch-universal.pkg https://github.com/pypa/hatch/releases/latest/download/hatch-universal.pkg
            ```
        2. 使用 macOS 的标准 [`installer`](https://ss64.com/osx/installer.html) 程序，指定下载的 `.pkg` 文件为安装源。使用 `-pkg` 参数指定包名，`-target /` 参数指定安装驱动器。文件将安装至 `/usr/local/hatch`，并在 `/etc/paths.d/hatch` 创建一项，指示 shell 添加 `/usr/local/hatch` 到 PATH。你需要在命令前加上 sudo 以获取写权限。

            ```
            sudo installer -pkg ./hatch-universal.pkg -target /
            ```
        3. 重启终端。
        4. 使用以下命令确认 shell 能够在 `PATH` 中找到并运行 `hatch` 命令：

            ```
            $ hatch --version
            <HATCH_LATEST_VERSION>
            ```

=== "Windows"
    === "图形界面安装器"
        1. 在浏览器中下载其中一个 `.msi` 安装文件：
              - [hatch-x64.msi](https://github.com/pypa/hatch/releases/latest/download/hatch-x64.msi)
        2. 运行你下载的文件并按照屏幕提示操作。
        3. 重启终端。
        4. 使用以下命令确认 shell 能够在 `PATH` 中找到并运行 `hatch` 命令：

            ```
            $ hatch --version
            <HATCH_LATEST_VERSION>
            ```
    === "命令行安装器"
        1. 使用标准 Windows [`msiexec`](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/msiexec) 程序下载并运行安装器，指定 `.msi` 文件作为源。使用 `/passive` 和 `/i` 参数发起无人值守的普通安装。

            === "x64"
                ```
                msiexec /passive /i https://github.com/pypa/hatch/releases/latest/download/hatch-x64.msi
                ```
            === "x86"
                ```
                msiexec /passive /i https://github.com/pypa/hatch/releases/latest/download/hatch-x86.msi
                ```
        2. 重启终端。
        3. 使用以下命令确认 shell 能够在 `PATH` 中找到并运行 `hatch` 命令：

            ```
            $ hatch --version
            <HATCH_LATEST_VERSION>
            ```

## 独立二进制文件

下载与你的平台和架构相应的归档文件后，将其中的可执行文件解压至你的 `PATH` 路径中，并重命名为 `hatch`。

=== "Linux"
    - [hatch-aarch64-unknown-linux-gnu.tar.gz](https://github.com/pypa/hatch/releases/latest/download/hatch-aarch64-unknown-linux-gnu.tar.gz)
    - [hatch-x86_64-unknown-linux-gnu.tar.gz](https://github.com/pypa/hatch/releases/latest/download/hatch-x86_64-unknown-linux-gnu.tar.gz)
    - [hatch-x86_64-unknown-linux-musl.tar.gz](https://github.com/pypa/hatch/releases/latest/download/hatch-x86_64-unknown-linux-musl.tar.gz)
    - [hatch-powerpc64le-unknown-linux-gnu.tar.gz](https://github.com/pypa/hatch/releases/latest/download/hatch-powerpc64le-unknown-linux-gnu.tar.gz)

=== "macOS"
    - [hatch-aarch64-apple-darwin.tar.gz](https://github.com/pypa/hatch/releases/latest/download/hatch-aarch64-apple-darwin.tar.gz)
    - [hatch-x86_64-apple-darwin.tar.gz](https://github.com/pypa/hatch/releases/latest/download/hatch-x86_64-apple-darwin.tar.gz)

=== "Windows"
    - [hatch-x86_64-pc-windows-msvc.zip](https://github.com/pypa/hatch/releases/latest/download/hatch-x86_64-pc-windows-msvc.zip)
    - [hatch-i686-pc-windows-msvc.zip](https://github.com/pypa/hatch/releases/latest/download/hatch-i686-pc-windows-msvc.zip)

## pip

Hatch 可从 PyPI 安装，可使用 [pip](https://github.com/pypa/pip) 安装：

```
pip install hatch
```

!!! warning
    该方法会修改你所选择安装的 Python 环境。为了避免依赖冲突，建议使用 [pipx](#pipx)。

## pipx

[pipx](https://github.com/pypa/pipx) 允许在隔离环境中全局安装 Python 应用：

```
pipx install hatch
```

## Homebrew

更多信息请查看 [配方说明](https://formulae.brew.sh/formula/hatch)：

```
brew install hatch
```

## Conda

更多信息请查看 [feedstock 项目](https://github.com/conda-forge/hatch-feedstock)：

```
conda install -c conda-forge hatch
```

或使用 [mamba](https://github.com/mamba-org/mamba)：

```
mamba install hatch
```

!!! warning
    该方法会修改你所选择安装的 Conda 环境。为了避免依赖冲突，建议使用 [pipx](#pipx) 或 [condax](https://github.com/mariusvniekerk/condax)。

## MacPorts

更多信息请查看 [MacPorts 页面](https://ports.macports.org/port/hatch/)：

```
sudo port install hatch
```

## Fedora

支持的最低版本为 37，目前处于 [Rawhide](https://docs.fedoraproject.org/en-US/releases/rawhide/) 开发中。

```
sudo dnf install hatch
```

## Void Linux

```
xbps-install hatch
```

## 构建系统可用性

Hatchling 是 Hatch 的 [构建后端](config/build.md#build-system)，你无需手动安装。版本信息请查看其 [更新日志](history/hatchling.md)。

[![打包状态](https://repology.org/badge/vertical-allrepos/hatchling.svg){ loading=lazy .off-glb }](https://repology.org/project/hatchling/versions)
```

如需将以上内容嵌入到文档项目中或转换为其他格式，也可以继续告诉我。