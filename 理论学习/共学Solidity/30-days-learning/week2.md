

## Day 8
### 代码



- [ ] 汇率动态变化的话，如何调整代码
	先用addCurrency()
	后利用day18 预言机（如 Chainlink）等方式获取实时数据：[[#预言机]]



- [x] 为什么要跟踪我们添加的所有货币代码，以便我们以后可以循环访问它们？
Solidity 不允许：
	`for (string key in rates) { ... } // ❌ 不行`
因此，我们必须手动维护一个列表：
`sptedCurs.push(_curCode);`
这样在前端（React、Remix、DApp）中就能：
`let list = contract.getSptedCur(); // ["USD", "EUR", "JPY", "GBP"]`


- [x] why 不能
	    //其他币种小费
	    function tipInCur(string memory _curCode) public payable {
	        //转换为ETH
	        uint256 ethAmount=msg.value*rates[_curCode]/(10**18);
	
	        require(ethAmount>0,"Tip amount must be greater than 0");
	
	        totalTipsReceived+=ethAmount;
	
	        tipperContributions[msg.sender]+=ethAmount;
	
	        tipsPerCurrency["ETH"]+=ethAmount;
	
	    }

而要转换才行
	
function tipInCurrency(string memory _currencyCode, uint256 _amount) public payable {
    require(conversionRates[_currencyCode] > 0, "Currency not supported");
    require(_amount > 0, "Amount must be greater than 0");

    uint256 ethAmount = convertToEth(_currencyCode, _amount);
    require(msg.value == ethAmount, "Sent ETH doesn't match the converted amount");

    tipperContributions[msg.sender] += msg.value;
    totalTipsReceived += msg.value;
    tipsPerCurrency[_currencyCode] += _amount;
}
==//注意：amount是外币数量;value是wei大小==

- [ ] 代码理解：
    function withdrawTips() public onlyOwner{

        uint256 contractBalance = address(this).balance; //balance是内置的？

        require(contractBalance > 0, "No tips to withdraw");
	   payable(owner).transfer(contractBalance);
    }
其中的payable(owner).transfer(contractBalance);
是什么意思
	“把合约里所有余额，发送给 owner 钱包。”

|代码|说明|
|---|---|
|`address(this)`|当前合约地址|
|`.balance`|内置属性，返回合约账户当前的 **ETH 余额（wei）**|
|`payable(owner)`|把普通地址 `owner` 转换为“可接收 ETH 的地址类型”|
|`.transfer(amount)`|Solidity 内置函数，用于发送 ETH（最多 2300 gas，失败则回滚）|
自 Solidity 0.8 起，推荐用：
`(bool success, ) = payable(owner).call{value: contractBalance}(""); require(success, "Transfer failed");`

因为：
- `transfer()` 在 gas 不够时会自动回滚；
    
- `call()` 更灵活，也更安全（官方推荐）。

- [x] 版本 / EVM 不兼容问题（**最可能的原因**）
> 报错信息：
> 
> `The execution might have thrown OR the EVM version used by the selected environment is not compatible with the compiler EVM version.`

👉 表示 **Remix 当前的 EVM 设置版本** 和你编译的版本（`pragma solidity ^0.8.0;`）不匹配。  
例如 Remix 默认跑在 “Shanghai” 或 “Cancun” EVM，而编译器目标可能是 “london” 或更老版本。


- [x] 单位换算错误
	本来转换的单位应该是wei，现在是eth；导致在tipToCur中的判断msg.value= = _ amount无法对应，即输入的msg.value是选择Eth作为单位的。所以！要先解决转换显示的问题！。。。（github源代码也是这个问题！会不会是我编辑器问题。）
	![[Pasted image 20251030021928.png]]
	对，编辑器问题，第二天再测验就好了；
	注意eth金额也只能输入整数；

- [x] 为什么接收其他小费要有require(msg.value == ethAmount, "Sent ETH doesn't match converted amount"); 这样一个判断？不可以直接接收吗？难道接收那方还要提前算好转换价格？那我要转换函数有何用
	提供给**前端应用**（App/网站）或**用户钱包**使用的；
	
	部署或调用合约时**手动输入 `msg.value`** 的这个测试过程，在实际的去中心化应用（dApp）中，通常是由**浏览器/前端界面和用户的钱包软件**自动处理和显示的。
	
### 知识点

- 如何处理 ETH 和 wei
- 为什么 Solidity 不支持小数
- 如何安全地进行转换数学
- 以及如何确保用户发送正确数量的 ETH

#### 货币

合约部署在以太坊区块链上。以太坊的原生货币是 **$\text{ETH}$**。

- **智能合约不能直接接收 $\text{USD}$、$\text{EUR}$ 等法币。**

#### wei
Solidity 不适用于小数。没有浮点数，没有分数。因此，当您在智能合约中处理以太币时，不是在处理 `0.5 ETH` 或 `1.25 ETH`.
实际上是<mark style="background: #FFF3A3A6;">在处理wei</mark>。

|单位|换算关系|示例|
|---|---|---|
|1 ETH|= 10¹⁸ wei|`1 ether == 1e18 wei`|
|1 Gwei|= 10⁹ wei|常用于 gas 价格（例如 20 gwei）|
|0.5 ETH|= 5×10¹⁷ wei|必须写成整数：`500000000000000000`|

所有货币数学都保留在链上的 wei 中。人类可读的 ETH 来自链下格式化。

否则，用eth会太小了，任何少于 1 ETH 的东西都会四舍五入为 0！！
	Solidity 的 `0.5 ether` 语法糖只是编译时转换为整数常量 `5e17 wei`。  
	并不意味着 Solidity 支持小数。

why？
因为 EVM（以太坊虚拟机）不支持浮点计算。  
在区块链中所有计算都必须是**确定的、可验证的**，而浮点运算存在：

- 精度误差
    
- 平台差异
    
- 不确定舍入
#### 预言机

预言机 = “链下数据的上链桥梁”。

在区块链中的作用：
	智能合约本身不能访问外部世界（比如汇率、天气、股票价格）。  
	所以必须依赖“预言机”提供这些数据。

最常用的是 ==Chainlink==。  
它会定期把现实世界的数据写入链上，使合约能安全读取。

#### call

为什么不直接使用 `.transfer()`?

好吧，`.call{value: ...}` 被认为是最安全、最灵活的发送 ETH 的方式：

- 即使接收者是智能合约，它也能正常工作（由于 gas 限制，某些合约拒绝`.transfer()`
- 它返回一个 `success` 标志，以便我们可以检查传输是否有效
- 它避免了与旧方法相关的一些限制和风险

#### transfer()

## Day 9
### 代码

- [ ] 没有内置数学函数吗？
	 eg power的存在就是比 ** 多一个 1 吗？

- [ ] onlyOwner这么常用为何不内置？

- [ ] 每个sol都有一个地址吗？

- [ ] ScientificCalculator scientificCalc = ScientificCalculator(sciCalculatorAddress);何意味？ScientificCalculator是sol引入之后的合约内置对象？
	**地址强制类型转换** ——你将一个地址转换为一个合约引用，以便直接与之交互
### 知识点

#### ABI

ABI 代表应用程序二进制接口 。你可以把它看作是合同的"通信协议"——它定义了当一方合同调用另一方时数据必须如何结构化。

#### revert报错
在合约部署（或构造函数执行）过程中，触发了 `revert` 操作。常见原因包括：
 构造函数中包含 `require` 或 `revert` 条件未满足；
 
## Day 10

### 代码

体重下降-奖励机制（后续或许可以调整为动态的？）

### 知识点
#### 事件event
[[week1#事件`event` 与 `emit`]]里有

## Day 11

### 知识点
#### openzeppelin


## Day 12
### 代码
为什么需要内部辅助函数？不可以直接发送事件吗？
	[[#逻辑分离]]，提取以便复用

缺失的部分：
	没有对 approve()进行抢先交易的保护
	
	这是一个在 ERC-20 中众所周知的漏洞，有人可能利用竞态条件来花费超出他们应有的金额。修复方法？使用 increaseAllowance()和 decreaseAllowance()，或者使用其他更安全的模式。

优化：
	import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
	contract MyToken is ERC20 {
	
	    constructor(uint256 initialSupply) ERC20("MyToken", "MTK") {
	
	        _mint(msg.sender, initialSupply * 10 ** decimals());
	
	    }
	
	}
### 知识点
#### ERC-20
ERC 代表以太坊请求评论
标准达成共识的方式

ERC-20？
那是 ERC 系列的第 20 个提案——而且恰好是定义了可替代代币在以太坊上应该如何行为的那一个。

ERC-20 制定了一个统一的接口——一种共享的语言——所有代币都应该使用。它涵盖了诸如：

**命名和显示**

你的代币必须有<mark style="background: #FFF3A3A6;">名称、符号和小数位数</mark>，以便钱包能正确显示它们。
	大多数代币使用 18 位小数——就像 ETH 一样。

**余额和供应量**

必须有 <mark style="background: #FFF3A3A6;">totalSupply()</mark>函数，以及一种方法来检查地址拥有多少代币，使用 <mark style="background: #FFF3A3A6;">balanceOf(address)</mark>。

**转账**

应该有一个<mark style="background: #FFF3A3A6;"> transfer()</mark>函数，允许人们从他们的钱包向其他人发送代币。

**批准和授权支出**

您的代币需要一个 <mark style="background: #FFF3A3A6;">approve()</mark>函数，以便用户可以让其他人（例如智能合约）代表他们花费代币。

以及一个 <mark style="background: #FFF3A3A6;">transferFrom()</mark>函数来实际执行这些已批准的转账。

**事件发射**

每当代币转移或权限被授予时，您的合约应<mark style="background: #FFF3A3A6;">发射 Transfer 和 Approval 事件</mark>。
	每个事件的作用如下：
	- Transfer：每当代币从一个地址转移到另一个地址时，这个事件就会触发。钱包和浏览器依赖这个事件来显示交易历史。
	- Approval：当有人授权另一个地址代表他们花费代币时，这个事件就会触发。

这些帮助钱包、DApps 和区块链浏览器追踪链上发生的事情。



#### 逻辑分离
不想在 transfer() 和 transferFrom() 等多个地方重复转账逻辑。
相反，我们将核心的余额变更逻辑提取到一个单独的函数 _ transfer() 中，并在需要的地方重用它。

## Day 13
### 代码
为什么
	if (!finalized && msg.sender != address(this) && initialTransferDone) { require(false, "Tokens are locked until sale is finalized"); }
不写成require(finalized && msg.sender != address(this) && initialTransferDone,"Tokens are locked until sale is finalized")？不是等价的吗？

为什么
	require(msg.value >= minPurchase, "Amount is below minimum purchase"); require(msg.value <= maxPurchase, "Amount exceeds maximum purchase"); 
不写成require(msg.value>=minPurchase&&msg.value<=maxPurchase,"Invalid ETH amount"); 会不会更加简洁？还是说前者更详细？

很多if条件句能否改成三元表达式

### 知识点

#### 重写
函数修饰 override
.super调用父类

#### receive()

它的功能是允许ETH流入，并将 ETH 直接路由到代币销售逻辑中。

`receive()` 函数是一个**特殊的回退函数**，在满足以下条件时被触发：

- 有人**直接**向合约地址发送 ETH
- 且**未指定**要调用的任何函数

通常若合约未定义该函数，外部直接转入 ETH 的交易会失败。

但在这种情况下，我们定义了 `receive()`，只要有人向该合约转入 ETH（即使只是从 MetaMask 或简单的钱包转账），合约都会在后台**自动调用`buyTokens()`完成购买流程**。

这意味着：

- 用户无需进入 dApp 的界面操作
- 无需手动调用 `buyTokens()` 函数
- 只需发送 ETH ，即可参与发售

虽是小改动，却显著改善用户体验——即使用户忘记调用购买函数，也仍能获得代币。

eg
```
receive() external payable {
    buyTokens();
}
```

## Day 14
### 代码

   //疑问：为何不用openzepplin
	
	    modifier onlyOwner() {
	
	        require(msg.sender == owner, "Not the box owner");
	
	        _;
	
	    }


为什么
	    function transferOwnership(address newOwner)external virtual override onlyOwner{
	
	        require(newOwner != address(0), "New owner cannot be zero address");
	
	        emit OwnershipTransferred(owner, newOwner);
	
	        owner = newOwner;
	
	    }
变量newOwner不加_

都是重复的动作：创建。
为什么不合到一起？将类别作为一个参数？

### 知识点
#### 接口interface
接口是一种只包含函数定义的合约——没有逻辑，没有存储，也没有状态变量。
用来规定强制执行规则


#### 抽象合约abstract
抽象合约是一个可以同时包含已实现的函数和未实现的（virtual）函数的合约

#### calldata
在传递字符串参数时，它在 gas 上更便宜。