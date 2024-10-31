# `foundry-toolchain` 概述

`foundry-toolchain` 可用于在 GitHub Actions 构建过程中安装 Foundry。

### 示例工作流程

```
on: [push]

name: test

jobs:
  check:
    name: Foundry 项目
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: recursive

      - name: 安装 Foundry
        uses: foundry-rs/foundry-toolchain@v1

      - name: 运行测试
        run: forge test -vvv

      - name: 运行快照
        run: forge snapshot
```

### 输入

| 名称 | 必需 | 默认值 | 描述 | 类型 |
|---|---|---|---|---|
| cache | 否 | true | 是否缓存 RPC 响应。 | bool |
| version | 否 | nightly | 要安装的版本，例如 nightly 或 1.8.8。注意：Foundry 目前只有 nightly 版本。 | string |
| cache-key | 否 | `${{ github.job }}` | 用于缓存的缓存键。 | string |
| cache-restore-keys | 否 | `[${{ github.job }}]` | 用于恢复缓存的缓存键。 | string[] |

### RPC 缓存

默认情况下，此操作与 Forge 的行为一致，并将所有 RPC 响应缓存到 `~/.foundry/cache/rpc` 目录中。这是为了加快测试速度并避免达到 RPC 提供商的速率限制。

缓存的逻辑如下：

- 始终加载最新的有效缓存，并始终使用更新的缓存创建新缓存。
- 当分叉测试没有变化时，缓存不变，但键会变，因为键是基于提交哈希的。
- 当分叉测试发生变化时，缓存和键都会更新。

如果你想禁用缓存（例如，因为你想实现自己的缓存机制），可以将 `cache` 输入设置为 `false`，如下所示：

```
- name: Install Foundry
  uses: foundry-rs/foundry-toolchain@v1
  with:
    cache: false
```

### 自定义缓存键

你可以通过使用 `cache-key` 和 `cache-restore-keys` 输入来自定义缓存键。此功能在你希望跨多个作业定制缓存共享策略时特别有用。确保每次执行的缓存键是唯一的，以防止冲突并保证成功保存缓存。

例如，如果你希望在两个不同的作业之间使用共享缓存，可以应用以下配置：

```
- name: Install Foundry
  uses: foundry-rs/foundry-toolchain@v1
  with:
    cache-key: custom-seed-test-${{ github.sha }}
    cache-restore-keys: |-
      custom-seed-test-
      custom-seed-
---
- name: Install Foundry
  uses: foundry-rs/foundry-toolchain@v1
  with:
    cache-key: custom-seed-coverage-${{ github.sha }}
    cache-restore-keys: |-
      custom-seed-coverage-
      custom-seed-
```

#### 删除缓存

你可以通过 GitHub Actions 用户界面删除缓存。只需转到你的仓库的 "Actions" 页面：

`https://github.com/<OWNER>/<REPO>/actions/caches`

然后，找到 "Management" 部分，点击 "Caches"。你将看到所有当前缓存的列表，可以通过点击垃圾桶图标删除它们。

#### 模糊测试

请注意，如果你在分叉测试中进行模糊测试，除非你设置了 [fuzz seed](https://book.getfoundry.sh/reference/config/testing#seed)，否则上述 RPC 缓存策略将不起作用。你可能还希望通过使用 [Multicall](https://github.com/mds1/multicall) 来减少 RPC 调用的次数。

### 总结

你可以将 Forge 和 Cast 命令的输出添加到 GitHub 步骤摘要中。摘要支持 GitHub 风格的 Markdown。

例如，要将 `forge snapshot` 的输出添加到摘要中，你可以将快照步骤更改为：

```
- name: 运行快照
  run: NO_COLOR=1 forge snapshot >> $GITHUB_STEP_SUMMARY
```