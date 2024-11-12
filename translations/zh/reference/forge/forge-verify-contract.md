## forge verify-contract

### 名称

forge-verify-contract - 在选定的验证 provider 上验证智能合约。

### 简介

``forge verify-contract`` [*options*] *address* *contract*

### 描述

在选定的验证 provider 上验证智能合约。

你必须提供：
- 合约地址
- 合约名称或合约的路径（请阅读下文）
如果是 Etherscan 验证，你还必须提供：
- 您的 Etherscan API 密钥，可以将其作为一个参数传递，或者设置 `ETHERSCAN_API_KEY`

要找到准确的编译器版本，运行 `~/.svm/x.y.z/solc-x.y.z --version` 并搜索版本字符串[此处](https://etherscan.io/solcversions) 中的 8 位十六进制数字。

合约的路径格式为 `<path>:<contract>`，例如 `src/Contract.sol:Contract`。

默认情况下，智能合约是以多文件的方式进行验证的。如果你想在验证前将合约扁平化，请传递 `--flatten`。

如果 `--flatten` 被传递 ，该命令将在验证前尝试编译扁平化的合约源代码。如果你不希望这样，请传递`--force`。

你可以用 `--constructor-args` 来指定 **ABI-编码** 构造函数参数。或者，你可以用`--constructor-args-path`指定一个包含 **空格分隔的** 构造函数参数的文件。
(注意，[cache](.../config/project.html#cache) 必须在配置中启用，后者才能发挥作用)。

### 选项

#### 验证合约选项

{{#include ../common/verifier-options.md}}

`--skip-is-verified-check`
&nbsp;&nbsp;&nbsp;&nbsp;即使合约已经被验证，也发送验证请求。

`--compiler-version` *version*  
&nbsp;&nbsp;&nbsp;&nbsp;用来构建智能合约的编译器版本。

&nbsp;&nbsp;&nbsp;&nbsp;要找到准确的编译器版本，运行 `~/.svm/x.y.z/solc-x.y.z --version`，其中 `x` 和 `y` 分别是主要和次要版本号，然后在 [此处](https://etherscan.io/solcversions) 搜索版本字符串中的 8 位十六进制数字 。

`--num-of-optimizations` *num*  
`--optimizer-runs` *num*      
&nbsp;&nbsp;&nbsp;&nbsp;用于建立智能合约的优化运行的数量。

`--constructor-args` *args*  
&nbsp;&nbsp;&nbsp;&nbsp;ABI 编码的构造函数参数。与 `--constructor-args-path` 冲突。

`--constructor-args-path` *file*  
&nbsp;&nbsp;&nbsp;&nbsp;包含构造函数参数的文件的路径。与 `--constructor-args` 冲突。

`--chain-id` *chain*  
`--chain` *chain*  
&nbsp;&nbsp;&nbsp;&nbsp;合约部署到的链的ID或名称。
&nbsp;&nbsp;&nbsp;&nbsp;默认：mainnet

`--flatten`  
&nbsp;&nbsp;&nbsp;&nbsp;表示是否在验证前对源代码进行扁平化的标志。

&nbsp;&nbsp;&nbsp;&nbsp;如果没有提供这个标志，将使用 JSON 标准输入代替。

`-f`  
`--force`  
&nbsp;&nbsp;&nbsp;&nbsp;在验证之前不要编译扁平化的智能合约。

{{#include ../common/retry-options.md}}

`--show-standard-json-input`  
&nbsp;&nbsp;&nbsp;&nbsp;该命令输出适合保存到文件并上传到区块浏览器进行验证的 JSON 格式数据。

`--watch`  
&nbsp;&nbsp;&nbsp;&nbsp;提交后等待验证结果。
&nbsp;&nbsp;&nbsp;&nbsp;自动运行 `forge verify-check`，直到验证失败或成功。

`--via-ir`
&nbsp;&nbsp;&nbsp;&nbsp;将 `viaIR` 设置为 true。
&nbsp;&nbsp;&nbsp;&nbsp;如果在部署时使用了 `--via-ir` 进行编译，请在此处也传递，以防止字节码不匹配。

{{#include project-options.md}}

{{#include common-options.md}}

### 例子

1. 在 Etherscan 上用 JSON 标准输入验证一个合约
    ```sh
    forge verify-contract <address> SomeContract --watch

2. 验证一个自定义 Sourcify 实例的合约
    ```sh
    forge verify-contract --verifier sourcify \
      --verifier-url http://localhost:5000 <address> SomeContract
    ```

3. 验证一个用 solc v0.8.11+commit.d7f03943 构建的扁平化合约：
    ```sh
    forge verify-contract --flatten --watch --compiler-version "v0.8.11+commit.d7f03943" \
      --constructor-args $(cast abi-encode "constructor(string,string,uint256,uint256)" "ForgeUSD" "FUSD" 18 1000000000000000000000) \
      <address> MyToken
    ```

4. 通过在文件中指定构造器参数来验证一个扁平化的合约：
    ```sh
    forge verify-contract --flatten --watch --compiler-version "v0.8.11+commit.d7f03943" \
      --constructor-args-path constructor-args.txt <address> src/Token.sol:MyToken
    ```
    其中 `constructor-args.txt` 包含以下内容：
    ```text
    ForgeUSD FUSD 18 1000000000000000000000
    ```

5. 部署后立即使用 Blockscout 验证合约（确保在 Blockscout 浏览器 URL 末尾添加 "/api?"）：
    ```sh
    forge create --rpc-url <rpc_https_endpoint> --private-key $devTestnetPrivateKey src/Contract.sol:SimpleStorage --verify --verifier blockscout --verifier-url <blockscout_homepage_explorer_url>/api? 
    ```

6. 使用 Oklink 验证合约
    ```sh
    forge verify-contract 0x8CDDE82cFB4555D6ca21B5b28F97630265DA94c4 Counter --verifier oklink --verifier-url https://www.oklink.com/api/v5/explorer/contract/verify-source-code-plugin/XLAYER  --api-key $OKLINK_API_KEY
    ```
    
7. 在部署时使用 Oklink 验证合约
    ```sh
    forge create Counter --rpc-url <rpc_https_endpoint> --verify --verifier oklink --verifier-url https://www.oklink.com/api/v5/explorer/contract/verify-source-code-plugin/XLAYER --etherscan-api-key $OKLINK_API_KEY --private-key $PRIVATE_KEY --legacy
    ```
    
### 另请参阅

[forge](./forge.md), [forge create](./forge-create.md), [forge flatten](./forge-flatten.md), [forge verify-check](./forge-verify-check.md)
