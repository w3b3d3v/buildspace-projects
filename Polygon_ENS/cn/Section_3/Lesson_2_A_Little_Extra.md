

### 🦴 获取所有域名

好吧 - 现在事情肯定正在成形。 我们还可以做更多的事情来让我们的合约变得更好并为我们的前端优化它，例如有一个超级简单的方法来获取所有铸造的域名——也许我们想展示所有已经铸造的史诗域名 ！！

最酷的部分是我们可以轻松地从我们的智能合约中返回这些数据！ 最好的部分 - 在区块链上读取交易是免费的🤑。 因此，我们不必担心支付gas费！

看看这个逻辑，看看我们如何做到这一点：
```solidity
// Add this at the top of your contract next to the other mappings
mapping (uint => string) public names;

// Add this anywhere in your contract body
function getAllNames() public view returns (string[] memory) {
  console.log("Getting all names from contract");
  string[] memory allNames = new string[](_tokenIds.current());
  for (uint i = 0; i < _tokenIds.current(); i++) {
    allNames[i] = names[i];
    console.log("Name for token %d is %s", i, allNames[i]);
  }

  return allNames;
}
```



很简单，对吧？ 你现在是一名 Solidity 专家，所以这对我们之前所做的应该很熟悉！

我们添加了一个映射来存储带有域名的 mint ID，并添加了一个`view`函数来遍历所有这些名称并将它们放入列表中以发送给我们。 不过少了一点！ 我们还需要设置映射数据！

将其添加到 `register` 函数的底部，就在 `_tokenIds.increment()` 之前：

```solidity
names[newRecordId] = name;
```



就像那样，您可以检索合同中创建的所有域🤘

### 💔 检查合约上的域名有效性

你可能想到的一件事是——“Raza 这很酷，但是如果有人试图创建一个 **真的** 长的域会发生什么？ 那太蹩脚了👎”

WELP - 你是对的！ 那太蹩脚了。 现在，我们正在 React 应用程序中使用 JavaScript 检查域是否有效。 这不是最好的主意，因为有人可以直接与我们的合约交互来创建一个无效的域！

在我们的合同中这样做实际上更有意义：

```solidity
function valid(string calldata name) public pure returns(bool) {
  return StringUtils.strlen(name) >= 3 && StringUtils.strlen(name) <= 10;
}
```



就像我们在 React 应用程序中所做的那样，我们需要检查域名是否在 3-10 个字符之间（这是为了帮助保持域名简短和干净）。 你应该把它设置成你想要的任何东西！ 也许你不喜欢字母 W？ 对于无效的名称，您只需要返回一个 `false`。 尝试添加您能想到的所有其他类型的块 :)

### 🤬 自定义错误

最新版本的 Solidity 中添加的最酷的功能之一是能够使用自定义错误消息。 这些非常有用，因为您不需要重复错误消息字符串，更重要的是，它们可以帮助我们在部署时节省 gas！ 因为谁愿意在汽油上花更多钱？ 创建它们非常简单：

```solidity
error Unauthorized();
error AlreadyRegistered();
error InvalidName(string name);
```



你可以把它们放在任何地方！ 使用它们：


```solidity
function setRecord(string calldata name, string calldata record) public {
  if (msg.sender != domains[name]) revert Unauthorized();
  records[name] = record;
}

function register(string calldata name) public payable {
  if (domains[name] != address(0)) revert AlreadyRegistered();
  if (!valid(name)) revert InvalidName(name);
  // Rest of register function remains unchanged
}
```



就是这样！ 如果你部署这个合约，你会比以前做更多的事情。 不要忘记复制并粘贴您的新合约地址和合约 ABI
到你的 React 应用程序！

看看你，省gas、高效，并在区块链上检查域有效性。 下一步是什么？ 你要**升级**你的智能合约吗？ 哈哈

### 🚨进度报告

在 `#progress` 中发布您出色的、省gas的、错误处理智能合约的屏幕截图！