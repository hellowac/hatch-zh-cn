# 共享

贡献代码的常规流程如下：

1. 检查是否存在相关问题
2. 复刻代码库并创建新分支
3. 进行修改
4. 确保格式、代码检查和测试均已通过。
5. 尽可能添加测试，以覆盖您添加的代码行。
6. 提交代码并发送拉取请求。

## 克隆仓库

克隆 `hatch` 存储库，`cd` 进入其中，并为您的贡献创建一个新的分支：

```bash
cd hatch
git switch -c add-my-contribution
```

## 运行测试

在开发时运行测试套件：

```bash
hatch test
```

运行带有覆盖率报告的测试套件：

```bash
hatch test --cover
```

运行扩展测试套件并覆盖：

```bash
hatch test --cover --all
```

## Lint

运行自动格式化：

```bash
hatch fmt
```

运行完整的 linting 和类型检查：

```bash
hatch fmt --check
hatch run types:check
```

## 文档

开始开发中的文档：

```bash
hatch run docs:serve
```

构建并验证文档网站：

```bash
hatch run docs:build-check
```