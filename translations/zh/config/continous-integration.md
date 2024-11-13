## 持续集成

### GitHub Actions

要使用 GitHub Actions 测试你的项目，这里有一个示例工作流程：

```yml
on: [push]

name: test

jobs:
  check:
    name: Foundry project
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: recursive

      - name: Install Foundry
        uses: foundry-rs/foundry-toolchain@v1
        with:
          version: nightly

      - name: Run tests
        run: forge test -vvv
```

### Travis CI

要使用 Travis CI 测试你的项目，这里有一个示例工作流程：

```yml
language: rust
cache:
  cargo: true
  directories:
    - $HOME/.foundry

install:
  - curl -L https://foundry.paradigm.xyz | bash
  - export PATH=$PATH:$HOME/.foundry/bin
  - foundryup -b master

script:
  - forge test -vvv
```

## GitLab CI

要使用 GitLab CI 测试你的项目，这里有一个示例工作流程：
注意：查看 [Policy](https://docs.gitlab.com/runner/executors/docker.html#how-pull-policies-work) 来获取远程镜像

```yml
variables:
  GIT_SUBMODULE_STRATEGY: recursive

jobs:
  image: ghcr.io/foundry-rs/foundry
  script:
    - forge install
    - forge test -vvv
```
