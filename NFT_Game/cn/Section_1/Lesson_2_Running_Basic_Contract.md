### 📝 编写我们的启动器智能合约

选择您最喜欢的代码编辑器并打开您设置Hardhat项目的目录!让我们开始编写智能合约吧。

我们希望删除Hardhat为我们生成的所有的初始代码。我们要自己写这些东西！所以请删除 `test`下的'`Lock.js` 文件。同时，删除`scripts`下的 `deploy.js` 。然后删除“ `contracts`的 `Lock.sol` 。**不要删除实际的文件夹!**

现在，我们开始写非功能性智能合约吧。如果你从未写过智能合约，也不用担心。**跟着我走就行了。你不懂的东西。在Discord中提问。**

在`contracts` 下面创建一个名为 `MyEpicGame.sol` 的文件。在使用Hardhat时，文件结构非常重要，所以在这里要小心!

注意:我推荐下载VSCode的[Solidity扩展](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)，它提供了很好的语法高亮显示。

我总是喜欢从一个非常基本的智能合约开始，只是为了让事情开始。

```javascript
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.17;

import "hardhat/console.sol";

contract MyEpicGame {
  constructor() {
    console.log("THIS IS MY GAME CONTRACT. NICE.");
  }
}
```

注意:有时VSCode本身会抛出实际上不是问题的错误。例如，它可能在Hardhat导入下面加下划线，并表示它不存在。发生这些情况是因为您的全局Solidity编译器没有在本地设置。如果你不知道如何解决这个问题，不要担心。暂时忽略这一点。另外，我建议你不要使用VSCode的终端，使用你自己的独立终端!如果编译器没有设置，VSCode终端有时会出现问题。

让我们一行一行地看。

```javascript
// SPDX-License-Identifier: UNLICENSED
```

只是一个花哨的评论。它被称为“SPDX许可证标识符”，可以自由谷歌它是什么:)。

```javascript
pragma solidity ^0.8.17;
```

This is the version of the Solidity compiler we want our contract to use. It basically says "when running this, I only want to use version 0.8.17 of the Solidity compiler, nothing lower. Note, be sure your compiler is set to 0.8.17 in `hardhat.config.js`.

这是我们希望智能合约使用的Solidity编译器的版本。它基本上是说:“当运行这个时，我只想使用Solidity编译器的0.8.17版本，不要更低。注意，确保你的编译器在`hardhat.config.js`中设置为0.8.17。

```javascript
import "hardhat/console.sol";
```

Hardhat允许我们在智能合约中做一些控制台日志。实际上，调试智能合约具有挑战性，但这是Hardhat提供给我们的好处之一，使工作更容易。

```javascript
contract MyEpicGame {
    constructor() {
        console.log("THIS IS MY GAME CONTRACT. NICE.");
    }
}
```

所以，智能合约有点像其他语言中的 `class` ，如果你见过的话!一旦我们第一次初始化这个智能合约，构造函数就会运行并打印出这一行。请让这条线说你想说的。希望你玩得开心点。

### 😲 我们如何运作它?

太棒了——我们有了一个智能合约!但是，我们不知道它是否有效。我们实际上需要:

1. 编译它。
2. 将它部署到我们的本地区块链。
3. 一旦它部署了，console.log就会运行。

我们只需要编写一个自定义脚本来为我们处理这三个步骤。

进入 `scripts` 目录，创建一个名为`run.js`的文件。这是`run.js`的内容:

```javascript
const main = async () => {
  const gameContractFactory = await hre.ethers.getContractFactory('MyEpicGame');
  const gameContract = await gameContractFactory.deploy();
  await gameContract.deployed();
  console.log("Contract deployed to:", gameContract.address);
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

`run.js`是我们测试智能合约的游乐场!

### 🤔 它是如何工作的?

**注意:VSCode可能会自动导入ethers。我们不需要进口 ethers之类的东西。因此，请确保没有导入任何内容。**

还是一行一行的看。

```javascript
const gameContractFactory = await hre.ethers.getContractFactory('MyEpicGame');
```

这实际上将编译我们的合同，并在 `artifacts` 目录下生成我们使用智能合约所需的必要文件。在你运行这个之后去看看吧:)。

```javascript
const gameContract = await gameContractFactory.deploy();
```

这是相当奇特的:)。

现在的情况是，Hardhat将为我们创建一个本地以太坊网络，但只是为了这个智能合约。然后，脚本完成后，它会破坏本地网络。每次运行合约时，它都是一个新的区块链。这有什么意义?这有点像每次刷新你的本地服务器，所以你总是从头开始，这使得调试错误很容易。

```javascript
await gameContract.deployed();
```

我们要等到我们的智能合约被正式挖掘并部署到我们当地的区块链!没错，Hardhat实际上在您的机器上创建了假的“矿工”，以尽力模仿实际的区块链。

我们的`constructor`构造函数在我们实际完全部署时运行!

```javascript
console.log("Contract deployed to:", gameContract.address);
```

最后，一旦它完成了部署了， `gameContract.address`会返回给我们部署智能合约的地址。这个地址就是我们在区块链上找到智能合约的地方。现在我们本地的区块链只有我们。这并不是很酷。

但是，在实际的区块链上有数百万的智能合约。因此，这个地址使我们可以方便地访问我们感兴趣的智能合约!当我们在后面几节课中部署到实际的区块链时，这将派上用场。

### 💨 运行它

在你运行这个之前，一定要在你的`hardhat.config.js`中把 `solidity: "0.8.4",`  改为 `solidity: "0.8.17",` 。

让我们运行它!打开终端并运行:

```javascript
npx hardhat run scripts/run.js
```

你应该看到你的 `console.log` 在智能合约中运行，然后你也应该看到合约地址打印出来!!


### 🎩 Hardhat & HRE

在这些代码块中，你会经常注意到我们使用了`hre.ethers`,但 `hre` 从来没有输入过任何地方?这是什么巫术?

直接从Hardhat文档中，你会注意到:

> Hardhat运行时环境，简称HRE，是一个包含Hardhat在运行任务、测试或脚本时公开的所有功能的对象。实际上，Hardhat就是HRE。
> 

So what does this mean? Every time you run a terminal command that starts with `npx hardhat` you are getting this `hre` object built on the fly using the `hardhat.config.js` specified in your code! This means you will never have to actually do some sort of import into your files like:

这是什么意思呢?每次你运行一个以 `npx hardhat` 开头的终端命令时，你都在使用你的代码中指定的 `hardhat.config.js` 动态构建这个' `hre` 对象!这意味着你永远不需要在你的文件中执行如下的导入操作:

`const hardhat = require("hardhat")`

**TL;DR -你会在我们的代码中看到很多“`hre` ，但从来没有导入过任何地方!查看[Hardhat文档](https://hardhat.org/advanced/hardhat-runtime-environment.html)了解更多信息!**

### 🚨 进度报告!

在Discord中的#progress中发布一个截图，将`npx hardhat run scripts/run.js` 的输出结果发出去:)。
