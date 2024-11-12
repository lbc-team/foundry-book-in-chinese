# forge

构建、测试、模糊测试、调试和部署 Solidity 合约

```bash
$ forge --help
```

```txt
用法: forge <COMMAND>

命令:
  bind               为智能合约生成 Rust 绑定
  bind-json          通过 JSON cheatcodes 生成项目结构的序列化/反序列化绑定
  build              构建项目的智能合约 [别名: b, compile]
  cache              管理 Foundry 缓存
  clean              移除构建产物和缓存目录 [别名: cl]
  clone              从 Etherscan 克隆合约
  compiler           编译器工具
  completions        生成 shell 完成脚本 [别名: com]
  config             显示当前配置 [别名: co]
  coverage           生成覆盖率报告
  create             部署智能合约 [别名: c]
  debug              作为脚本调试单个智能合约 [别名: d]
  doc                为项目生成文档
  eip712             为给定文件中的结构生成 EIP-712 结构编码
  flatten            将源文件及其所有导入扁平化为一个文件 [别名: f]
  fmt                格式化 Solidity 源文件
  geiger             检测项目及其依赖中不安全 cheat codes 的使用
  generate           生成脚手架文件
  generate-fig-spec  生成 Fig 自动补全规范 [别名: fig]
  help               打印此消息或给定子命令的帮助
  init               创建一个新的 Forge 项目
  inspect            获取关于智能合约的专门信息 [别名: in]
  install            安装一个或多个依赖 [别名: i]
  remappings         获取项目的自动推断重映射 [别名: re]
  remove             移除一个或多个依赖 [别名: rm]
  script             作为脚本运行智能合约，构建可以在链上发送的交易
  selectors          函数选择器工具 [别名: se]
  snapshot           创建每个测试的 gas 使用快照 [别名: s]
  soldeer            Soldeer 依赖管理器
  test               运行项目的测试 [别名: t]
  tree               显示项目依赖图的树状可视化 [别名: tr]
  update             更新一个或多个依赖 [别名: u]
  verify-bytecode    验证已部署的字节码与其在 Etherscan 上的源代码 [别名: vb]
  verify-check       检查在 Etherscan 上的验证状态 [别名: vc]
  verify-contract    在 Etherscan 上验证智能合约 [别名: v]

选项:
  -h, --help     打印帮助
  -V, --version  打印版本

在书中找到更多信息:
http://book.getfoundry.sh/reference/forge/forge.html
```