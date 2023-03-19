### 📝编写我们的初始合约

*注意：如果您已经从我们过去运行的`WavePortal`项目中了解如何执行本节中的很多内容，那就太棒了！ 你会很快完成这个:)。 其中大部分操作都是重复的*。

先让我们做一些清理工作。

现在在您最喜欢的代码编辑器中打开项目的代码。我最喜欢的是 VSCode！我们想删除为我们生成的所有蹩脚的初始代码。我们不需要这些，因为我们是专业人士！

继续并删除`test`下的文件`Lock.js`；另外，删除`scripts` 下的 `deploy.js`；然后，删除`contracts`下的`Lock.sol`。**不要删除实际的文件夹！**

现在，在 VSCode 中打开项目，让我们开始编写我们的 NFT 合约。 如果您从未编写过智能合约，请不要担心。**请跟上我们，谷歌搜索困惑你的问题或者在 Discord 中提问。**

在`contracts`目录下创建一个名为`MyEpicNFT.sol`的文件。使用`Hardhat`时文件目录结构非常重要，所以，请注意！

*注意：我建议为 VSCode 下载 [Solidity 扩展](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)，它提供了很好的语法高亮显示。*

我总是喜欢从一份非常基础的合约开始，这样做只是为了让事情顺利进行。

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.17;

import "hardhat/console.sol";

contract MyEpicNFT {
    constructor() {
        console.log("This is my NFT contract. Whoa!");
    }
}
```
*注意:有时`VSCode` 会提示非真正的错误，例如，它可能会在`Hardhat`导入时加下划线，说它不存在。这是因为您的全局`Solidity`编译器没有在本地设置。如果你不知道如何解决这个问题，不要担心，现在先忽略这些。另外，我建议你不要使用VSCode的终端，使用你自己的独立终端!如果编译器没有设置，VSCode终端有时会出现问题。*

让我们逐行来分析。

```solidity
// SPDX-License-Identifier: UNLICENSED
```
只是一个被称为`SPDX许可证标识符`，你可以在[这里](https://spdx.org/licenses/)阅读更多关于它的信息。

```solidity
pragma solidity ^0.8.17;
```
这是我们希望合约使用的`Solidity`编译器版本。它说的是“**编译合约时，只能使用的版本不低于 0.8.17 但不高于 0.9.0 的 Solidity 编译器**”。请注意，确保在`hardhat.config.js`中设置了相应地的编译器（例如 0.8.17），比如：

```solidity
import "hardhat/console.sol";
```
Hardhat 给了我们一些魔力，允许我们在合约中记录一些控制台日志。实际上，调试智能合约具有挑战性，但这正是 Hardhat 的优势之处，使工作变得更加容易。

```solidity
contract MyEpicNFT {
    constructor() {
        console.log("This is my NFT contract. Whoa!");
    }
}
```
所以，智能合约有点像其他语言的`类`，如果你见过的话！ 一旦我们初始化这个合约，构造函数就会运行并输出以上数据行。可以随意修改上方的字段行，祝你玩得开心。

### 😲我们如何运行？

太棒了——我们已经有一份智能合约！但是，我们还不知道它是否有效。实际上我们需要：

1. 编译它。
2. 将其部署到我们的本地区块链。
3. 一旦准备就绪，`console.log`就会运行。

我们只需要编写一个自定义脚本来完成以上三个步骤。

进入 `scripts` 目录并创建一个名为 `run.js` 的文件，下面是 `run.js` 的内容：
```javascript
const main = async () => {
  const nftContractFactory = await hre.ethers.getContractFactory('MyEpicNFT');
  const nftContract = await nftContractFactory.deploy();
  await nftContract.deployed();
  console.log("Contract deployed to:", nftContract.address);
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
### 🤔它是如何工作的?

*注意:VSCode可能会自动导入`ethers库`，无需手动。*

我们还是逐行来分析。
```javascript
const nftContractFactory = await hre.ethers.getContractFactory("MyEpicNFT");
```
这实际上会编译我们的合约，并在`artifacts`目录下生成我们使用合约的必要文件。在你运行之后去看看吧:)。

```javascript
const nftContract = await nftContractFactory.deploy();
```
这是真的很神奇。

现在的情况是，`Hardhat`将为我们创建一个本地Ethereum网络，但仅仅只是为了运行这个合约。然后，脚本完成后，它会撤销本地网络。每次运行合约，它都是一个新的区块链。这有什么意义？这有点像每次重置你的本地服务器，所以你总是从头开始，这使得调试错误变得很容易。

```javascript
await nftContract.deployed();
```
我们需要等待我们的合约被正式采用并部署到本地区块链！没错，hardhat 实际上是在你的机器上创建了虚拟的“矿工”，以尽量模仿真实的区块链。

我们的`构造函数`会在我们完成部署时运行！

```javascript
console.log("Contract deployed to:", nftContract.address);
```
最后，一旦它被部署，`nftContract.address` 一般会为我们提供已部署合约的地址。这个地址是我们可以在区块链上面真实找到的的合约，但现在只是部署在我们自己的本地区块链上。所以，这并不是那么酷。

但是，真实的区块链上有数百万个合约。所以，这个地址让我们可以轻松访问我们感兴趣的合约！当我们在后续的课程部署到真实的区块链时，这会派上用场。

### 💨运行项目

在运行此之前，请务必将 `hardhat.config.js` 中的 `solidity: "0.8.4",` 更改为 `solidity: "0.8.17",`。

然后继续运行！打开你的终端并运行：
```bash
npx hardhat run scripts/run.js
```
你应该看到你的 `console.log` 从合约中运行，然后你还应该看到输出的合约地址！！！ 

这是我看到的：

![](https://i.imgur.com/CSBimfv.png)

### 🎩Hardhat & HRE

在这些代码块中，您会经常注意到我们使用了 `hre.ethers`，但是 `hre` 从来没有导入过任何地方？这是什么类型的魔法？

直接从 Hardhat 文档本身，您会注意到这一点：

> Hardhat 运行时的环境，简称 HRE，是一个包含 Hardhat 在运行任务、测试或脚本时公开的所有功能的对象。实际上，Hardhat 就是 HRE。

那么这是什么意思？好吧，每次运行以`npx hardhat`开头的终端命令时，您都会使用代码中指定的`hardhat.config.j` 即时构建这个 `hre` 对象！这意味着您永远不必实际对文件进行某种导入，例如：

`const hardhat = require("hardhat")`

**TL;DR - 你会在我们的代码中看到很多 `hre`，但从未导入任何地方！查看这个很棒的 [Hardhat 文档](https://hardhat.org/advanced/hardhat-runtime-environment.html) 以了解更多信息！**

### 🚨进度证明提交

在 `#progress` 频道中发布使用`npx hardhat run scripts/run.js`命令输出的截图 :)。












