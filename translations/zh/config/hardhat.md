## 与 Hardhat 集成

可以让你的 Foundry 项目与 [Hardhat](https://hardhat.org/) 一起工作。本文假设你的系统中已安装了 Foundry 和 node。本文还假定你对 Foundry 和 Hardhat 都很熟悉。

### 为什么这不能开箱即用？

默认情况下，Hardhat 希望将库安装在 `node_modules` 中，这是所有 NodeJS 依赖项的默认文件夹。 Foundry 希望它们在 `lib` 中。 当然 [我们可以配置 Foundry](../reference/config/overview.md) 但不容易配置到 `node_modules` 的目录结构。

因此，推荐的设置是使用 [hardhat-preprocessor](https://www.npmjs.com/package/hardhat-preprocessor)。 当正确安装和使用 hardhat-foundry 时，Hardhat 将使用与 Foundry 相同的合约目录，并且能够使用使用 forge install 安装的依赖项。

本文将涵盖以下两种情况：

1. 将 Hardhat 添加到 Foundry 项目中，以及
2. 将 Foundry 添加到 Hardhat 项目中。

### 给我看看示例 repo！

[Enjoy！](https://github.com/foundry-rs/hardhat-foundry-template)

如果你想将其适配到已有的 Foundry 项目或了解其工作原理，请阅读以下内容：

### 指示

在你的 Foundry 项目工作目录中：

1. `npm init -y` - 这将设置一个 `package.json` 文件。
2. `npm i --save-dev hardhat` - 将 Hardhat 作为开发依赖项安装到你的项目中。
3. `npx hardhat init` - 在相同目录中初始化你的 Hardhat 项目，并选择 “**创建一个空的 hardhat.config.js**” 选项。这将创建一个基本的 `hardhat.config.js` 文件。
4. `npm i --save-dev @nomicfoundation/hardhat-foundry @nomicfoundation/hardhat-toolbox` - 这将安装 hardhat-foundry 插件和 Hardhat 工具包插件，后者是运行 Hardhat 测试所需的所有基本依赖项的组合。

为使插件工作，你的 hardhat.config.js 文件应如下所示：

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("@nomicfoundation/hardhat-foundry");
/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.19",
};
```

5. 默认情况下，Foundry 项目附带一个简单的 `Counter.sol` 合约和一些测试。在 `test` 目录中创建一个名为 `Counter.t.js` 的文件，与默认的 `Counter.t.sol` 文件并列。
6. 将以下代码添加到 `Counter.t.js` 文件中：

```javascript
const { expect } = require("chai");
const hre = require("hardhat");
const { loadFixture } = require("@nomicfoundation/hardhat-toolbox/network-helpers");

describe("Counter contract", function () {
  async function CounterLockFixture() {
    const counter = await ethers.deployContract("Counter");
    await counter.setNumber(0);

    return { counter };
  }

  it("Should increment the number correctly", async function () {
    const { counter } = await loadFixture(CounterLockFixture);
    await counter.increment();
    expect(await counter.number()).to.equal(1);
  });

  // This is not a fuzz test because Hardhat does not support fuzzing yet.
  it("Should set the number correctly", async function () {
    const { counter } = await loadFixture(CounterLockFixture);
    await counter.setNumber(100);
    expect(await counter.number()).to.equal(100);
  });
});
```

这段代码将执行与默认的 `Counter.t.sol` 文件相同的测试。

就是这样！
你可以在同一个 `test` 目录中创建 Hardhat 和 Foundry 测试，并分别使用 `npx hardhat test` 和 `forge test` 运行它们。
查看 [Hardhat 的文档](https://hardhat.org/docs)以了解更多信息。

### 将 Foundry 添加到 Hardhat 项目中

在你的 Hardhat 项目工作目录中：

1. `npm i --save-dev @nomicfoundation/hardhat-foundry` - 安装 hardhat-foundry 插件。
2. 将 `require("@nomicfoundation/hardhat-foundry");` 添加到你的 `hardhat.config.js` 文件顶部。

> ℹ️ **注意**
> 如果你的目录尚未初始化为 git 存储库，则步骤 3 将无法正常工作。如果尚未初始化，请运行 `git init`。

3. 在终端中运行 `npx hardhat init-foundry`。这将基于你的 Hardhat 项目的现有配置生成一个 `foundry.toml` 文件，并安装 `forge-std` 库。

Hardhat 现在将在相同目录中设置一个基本的 Foundry 项目，并在 `foundry.toml` 文件中进行一些配置，以确保 Foundry 知道在哪里查找你的合约、测试和依赖项。你始终可以通过编辑 `foundry.toml` 文件来更改这些配置。