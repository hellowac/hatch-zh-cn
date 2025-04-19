# 命令行使用（CLI usage）

-----

## 冗余等级（Verbosity）

输出的详细程度完全由 `-v` / `--verbose`（环境变量 `HATCH_VERBOSE`）与 `-q` / `--quiet`（环境变量 `HATCH_QUIET`）这两个 [根选项](reference.md#hatch) 控制。

各级别的具体定义详见：[终端输出配置](../config/hatch.md#terminal)。

## 项目识别（Project awareness）

无论当前所处的 [模式](../config/hatch.md#mode) 如何，Hatch 在 [进入环境](../environment.md#entering-environments) 或 [执行命令](../environment.md#command-execution) 时，始终会切换到项目的根目录。

## Tab 补全（Tab completion）

补全功能通过生成一个脚本并将其加入到 shell 的启动流程中实现。

完成配置后，需重新启动一个 shell 实例以使变更生效。

=== "Bash"
    将脚本保存到任意位置：

    ```console
    _HATCH_COMPLETE=bash_source hatch > ~/.hatch-complete.bash
    ```

    然后在 `~/.bashrc`（macOS 上为 `~/.bash_profile`）中引入该脚本：

    ```bash
    . ~/.hatch-complete.bash
    ```

=== "Z shell"
    将脚本保存到任意位置：

    ```console
    _HATCH_COMPLETE=zsh_source hatch > ~/.hatch-complete.zsh
    ```

    然后在 `~/.zshrc` 文件中引入该脚本：

    ```zsh
    . ~/.hatch-complete.zsh
    ```

=== "fish"
    将脚本直接保存至 `~/.config/fish/completions` 目录下：

    ```console
    _HATCH_COMPLETE=fish_source hatch > ~/.config/fish/completions/hatch.fish
    ```
