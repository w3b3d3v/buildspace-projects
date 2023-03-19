
### 👶 写个合同吧

*注意：如果您已经从 ["WavePortal" 项目](https://app.buildspace.so/projects/CO02cf0f1c-f996-4f50-9669-cf945ca3fb0b) 了解如何执行本节中的很多内容，我们之前运行过，厉害！ 你会很快通过这个:)。 其中大部分是重复的*。 

太棒了，我们做到了。 我们准备开始编写智能合约。 我们将直接进入我们的项目。 在你喜欢的文本编辑器中打开项目文件夹，我喜欢 VSCode。 如果您从未编写过智能合约，请不要担心。 **请跟随。 谷歌你不明白的东西。 在 Discord 中提问。**

在 `contracts` 目录下创建一个名为 `Domains.sol` 的文件。 使用 Hardhat 时文件结构非常重要，所以，请注意！

注意：我建议为 VSCode 下载 [Solidity 扩展](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)，它可以很好地突出显示语法。

我总是喜欢从一份非常基本的合约开始，只是为了让事情顺利进行。
```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.10;

import "hardhat/console.sol";

contract Domains {
  constructor() {
    console.log("THIS IS MY DOMAINS CONTRACT. NICE.");
  }
}
```



注意：有时 VSCode 本身会抛出实际上不是问题的错误。 例如，它可能会在 hardhat import 下划线并表示它不存在。 发生这种情况是因为您的全局 Solidity 编译器未在本地设置。 如果您不知道如何解决这个问题，请不要担心。 暂时忽略这个。 另外，我建议你不要使用VSCode的终端，使用你自己的独立终端！ 如果未设置编译器， VSCode 终端会出现问题。

让我们在这里逐行解释。

```solidity
// SPDX-License-Identifier: UNLICENSED
```



只是一个花哨的评论。 它被称为“SPDX 许可证标识符”，请随意谷歌它是什么:)。

```solidity
pragma solidity ^0.8.10;
```



这是我们希望合约使用的 Solidity 编译器的版本。 它基本上是说“运行它时，我只想使用 0.8.10 版本的 Solidity 编译器，不能再低了。注意，确保你的编译器在` hardhat.config.`js 中设置为 0.8.10。
```solidity
import "hardhat/console.sol";
```



Hardhat 给了我们一些魔法，可以在我们的合约中做一些控制台日志。 调试智能合约实际上具有挑战性，但这是 Hardhat 为我们提供的让生活更轻松的好东西之一。

```solidity
contract Domains{
    constructor() {
        console.log("THIS IS MY DOMAIN CONTRACT. NICE.");
    }
}

```


所以，智能合约有点像其他语言的“类”，如果你见过的话！ 一旦我们第一次初始化这个合约，构造函数就会运行并打印出那一行。 请让那行说任何你想说的。 玩得开心一点。

### 😲 我们如何运行它？

太棒了——我们有一个智能合约！ 但是，我们不知道它是否有效。 我们实际上需要：

1. 编译它。
2. 将其部署到我们的本地区块链。
3. 一旦它在那里，`console.log` 就会运行。

我们只是要编写一个自定义脚本来为我们处理这 3 个步骤。

进入 `scripts` 目录并创建一个名为 `run.js` 的文件。 这就是 `run.js` 里面的内容：
```jsx
const main = async () => {
  const domainContractFactory = await hre.ethers.getContractFactory('Domains');
  const domainContract = await domainContractFactory.deploy();
  await domainContract.deployed();
  console.log("Contract deployed to:", domainContract.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```



`run.js` 是我们玩弄合约的游乐场！

### 🤔它是如何工作的？

**注意：VSCode 可能会自动导入以太币。 我们不需要导入以太币或任何东西。 所以，请确保不要导入任何东西。**

同样，在这里逐行进行解释。
```jsx
const domainContractFactory = await hre.ethers.getContractFactory('Domains');
```



这实际上会编译我们的合约，并在 `artifacts` 目录下生成我们使用合约所需的必要文件。 运行后去检查它:)。

```jsx
const domainContract = await domainContractFactory.deploy();
```



这很花哨:)。

这里发生的是 Hardhat 将为我们创建一个本地以太坊网络，**但只是为了这个合约**。 然后在脚本完成后，它将破坏该本地网络。 所以，每次你运行合约时，它都会是一个新的区块链。 重点是什么？ 这有点像每次都刷新您的本地服务器，因此您始终从头开始，这使得调试错误变得容易。
```jsx
await domainContract.deployed();
```



我们将等到我们的合约被正式开采并部署到我们的本地区块链！ 没错，hardhat 实际上是在你的机器上创建了假的“矿工”，以尽量模仿实际的区块链。

我们的“构造函数”在我们实际完全部署时运行！

```jsx
console.log("Contract deployed to:", domainContract.address);
```



最后，一旦它被部署，`domainContract.address` 基本上会为我们提供已部署合约的地址。 这个地址是我们如何在区块链上实际找到我们的合同。 现在在我们本地的区块链上只有我们自己。 所以，这不是那么酷。

但是，实际的区块链上有数百万个合约。 所以，这个地址让我们可以轻松访问我们感兴趣的合同！ 当我们在几节课中部署到实际的区块链时，这会派上用场。

### 💨 运行它

在运行此之前，请务必在您的 hardhat.config.js 中将`solidity: "0.8.4",` 更改为`solidity: "0.8.10",`。

让我们运行吧！ 打开你的终端并运行：
```bash
npx hardhat run scripts/run.js
```



您应该看到您的 `console.log` 是从合约中运行的，然后您还应该看到打印出的合约地址！！！

### 🎩 Hardhat和 HRE

在这些代码块中，您会经常注意到我们使用了`hre.ethers`，但是从来没有在任何地方导入过`hre`？ 这是什么类型的魔法？

直接来自 Hardhat 文档本身，您会注意到这一点：

> The Hardhat Runtime Environment, or HRE for short, is an object containing all the functionality that Hardhat exposes when running a task, test or script. In reality, Hardhat is the HRE.
> 



那么这是什么意思？ 每次运行以  `npx hardhat  `开头的终端命令时，您都会使用代码中指定的  `hardhat.config.js ` 即时构建这个  `hre  `对象！ 这意味着您永远不必实际对文件进行某种导入，例如：
`const hardhat = require("hardhat")`



**TL;DR - 您会在我们的代码中看到很多 `hre` ，但从未导入任何地方！ 查看 [Hardhat 文档](https://hardhat.org/advanced/hardhat-runtime-environment.html) 了解更多信息！**

### 🚨 进度报告！

使用 `npx hardhat run scripts/run.js` 的输出在#progress 中发布屏幕截图:)