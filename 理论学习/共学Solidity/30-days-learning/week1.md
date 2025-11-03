## 总览
👭第一周学习委员，开始接单啦～
https://www.notion.so/0xherstory/291064212688800cbc8df13ea1ec5502?source=copy_link

✨助教：带大家认识我们的四位助教！@iceฉันคิดถึง @中羊 @segment7 @Rebecca🌞 

🪐导师：@Bala🌍  + Sneha（on TG）

## 直播

mapping（）
和直接赋值的差别
	一个是映射“规则”
	一个是在映射中查找值

不熟悉的函数去官方网站查询了解：
https://www.soliditylang.org/
## Day 1

### **（1）SPDX许可证标识符**

每个Solidity文件合同的第一行是许可证标识符。这是指定与代码关联的法律权限和限制的标准方法。

```solidity
// SPDX-License-Identifier: MIT
```

SPDX代表软件包数据交换，这只是在代码中指示许可证的正式方式。
“MIT”是指麻省理工学院许可证，这是最宽松的开源许可证之一。

### **（2）Pragma 指令 - 设置 Solidity 版本**

```solidity
pragma solidity ^0.8.0;
```

在 Solidity 开始读取合约之前，我们需要告诉它应该使用**哪个编译器版本**。这就是这条线的作用。

- `pragma`: 编译器指令关键字
- `^0.8.0`: 表示使用0.8.0或更高版本,但低于0.9.0
- 不同版本的编译器可能有不同的特性和行为
- 0.8.0版本开始,自动检查整数溢出,更安全!
#### 不同写法
不带^：是固定版本写法
Caret (^) 表达式：表示从 0.8.0 起、但 < 0.9.0 ==(推荐用法)==
<>范围：根据意思来
	eg  pragma solidity >=0.8.0 <0.9.0;


###  （3） 定义智能合约

```soildity

contract ClickCounter { 
	// 合约内容在这里
 }
```

这是我们声明智能合约的地方。

**关键点:**
- `contract`关键字类似于其他语言中的`class`
- 合约是Solidity代码的基本单位
- 合约名称应该使用大驼峰命名法(PascalCase)
- 一个文件可以有多个合约,但通常一个文件一个主合约

### **(4）声明状态变量**

```solidity
uint256 public counter;
```

此行创建名为 `counter` 的**状态变量** 。

**扩展知识点：**

1. **数据类型**

除了`uint256`,还有`int`、`bool`、`address`、`string`等类型

1. **可见性修饰符**

`public`、`private`、`internal`、`external`控制访问权限


### 拓展
#### require
`require` 是 Solidity 中用于验证条件的一个函数。它的作用是检查一个条件是否为 `true`，如果条件不满足，则会 **抛出异常** 并回滚当前交易。`require` 是一个非常重要的安全功能，它能防止无效的操作、非法输入或不符合预期的行为发生。

**基本语法：**

`require(正确condition, "Error message");`

- `condition`：布尔值表达式，若为 `false`，则会抛出错误并回滚。
    
- `"Error message"`：可选的错误消息，如果不满足condition，用于描述失败的原因。
    

**require和try-catch的区别**
`require` 用来“防止错误发生”；  
`try-catch` 用来“处理已经发生的错误”
#### 无法部署 not payable

在 Solidity 中，只有标记为 `payable` 的函数才能接收以太币。如果你不需要合约接收以太币，可以忽略 `payable` 关键字。如果你希望合约能够接收以太币，需要在函数签名中加入 `payable` 关键字。

例如：
`function deposit() public payable {     // 合约可以接收以太币 }`

#### 函数修饰符

- **`view`**：表示该函数不会修改合约的状态变量，只能读取状态变量，==不会消耗 gas==。
    
- **`pure`**：表示该函数不会读取或修改合约的任何状态变量。`pure` 函数完全依赖于输入参数进行计算，不与合约的状态交互，因此它不会消耗 gas。
    
- **`payable`**：[[#无法部署 not payable]]

#### **可见性修饰符**

Solidity 中的 **可见性修饰符** 用来控制合约中变量和函数的访问权限。常用的可见性修饰符包括 `public`、`private`、`internal` 和 `external`

- **`public`**：公有的，表示==任何==外部或内部的用户都可以访问或调用。对于函数，`public` 表示可以通过交易或者其他合约调用该函数；对于变量，`public` 表示可以从合约外部读取该变量的值（Solidity 会自动生成 getter 函数）。
    
- **`private`**：私有的，表示该函数或变量只能在==当前合约内部==访问。即使是继承的子合约也不能访问该函数或变量。
    
- **`internal`**：内部的，表示该函数或变量只能在当前合约==或其子合约中==访问。这是一个常用的修饰符，用于控制继承和子合约的访问权限。
    
- **`external`**：外部的，表示该函数==只能通过合约外部调用==。通常用于接口函数或其他合约的调用。

**virtual**：
会告诉 Solidity：

> “注意，如果有其他合约继承了这个合约，这个函数是可以被重新修改的。”
#### 数据类型
除了`uint256`,还有`int`、`bool`、`address`、`string`等类型

address indexed
## Day 2


需求：
![[Pasted image 20251021162016.png]]

约定俗称的命名：
局部变量和私有函数命名前面加_

#### 存储类型
**Storage** (存储）:==永久==存储在区块链上的数据（例如姓名和简介）。

**Memory** （内存）:仅在函数运行时存在的==临时==存储空间。
	eg. string memory _ name

注意：
组合功能可以使<mark style="background: #FFF3A3A6;">合约更短</mark>，但如果操作不当，也会<mark style="background: #FFF3A3A6;">增加 gas 成本</mark>。

eg
```solidity
function saveAndRetrieve(string memory _name, string memory _bio) public returns (string memory, string memory) {
    name = _name;
    bio = _bio;
    return (name, bio);
}
```
![[Pasted image 20251021164628.png]]


#### gas的计算

总Gas = 基础Gas + 运算Gas

**基础Gas**：每笔交易的固定开销，比如：
- 发起一笔交易（无论做什么）要消耗约 **21,000 Gas**。

**运算Gas**

每个 EVM 指令有固定价格：

|操作|消耗 (约值)|
|---|---|
|加法 `ADD`|3 gas|
|存储 `SSTORE`|20,000 gas（写入新值）|
|读取 `SLOAD`|100 gas|
|创建合约|~32,000 gas|
|调用合约|~700 gas|

实际支付的费用是：Gas Used × Gas Price = 实际费用

在以太坊上，Gas Price 会根据网络繁忙程度浮动

### 问题
为何会出现infinite gas**
	编译器或 Remix 无法在**静态分析阶段**估出交易的 gas 消耗。
	出现情况：①修改状态变量但只call ②存在循环/递归 ③一场导致失败

**transact和call的区别是什么**？

|项目|`call`|`transact`|
|---|---|---|
|**是否上链**|❌ 否，仅本地执行|✅ 是，会记录到区块链|
|**是否消耗 gas**|❌ 否（模拟执行）|✅ 是（由发送者支付）|
|**是否能修改状态**|❌ 否|✅ 能|
|**是否能获取返回值**|✅ 是|一般返回交易哈希（不直接返回值）|
|**典型用途**|查询函数（view/pure）|写入函数（改变状态）|

transact是在部署阶段执行的（动态的）
call是静态阶段

## Day 3

### 代码

#### 优化方向
防止重复选；用byte32存储节约gas；添加事件记录；统一函数命名
- **重复候选人问题**
    
    - 目前 `addCandidateNames` 没有限制重复添加同名候选人，会造成混乱。  
        ✅ 解决：用 `mapping(string => bool)` 检查是否已存在。
        
- **字符串作为键的 Gas 成本较高**
    
    - Solidity 中字符串比较、哈希都昂贵。  
        ✅ 优化：可以用 `bytes32` 存储候选人名（若无需展示原文），或继续用 `string` 但最小化操作。
        
- **投票重复问题**
    
    - 每个地址可以无限次调用 `vote`。  
        ✅ 解决：加入 `mapping(address => bool)` 防止重复投票。
        
- **事件机制缺失**
    
    - 没有事件记录添加候选人或投票，区块链上无法追踪操作。  
        ✅ 添加事件 `CandidateAdded` 和 `Voted`。
        
- **函数命名和可见性优化**
    
    - 建议统一命名风格，遵循 Solidity 风格指南。  
        ✅ 使用驼峰式、加上 `external` / `public` / `view` / `pure`。
### 知识点
#### 数组

数据类型[] 名字；
.push添加
#### 映射

mapping(数据类型1=>数据类型2)；

利用mapping(数据类型=>bool)可以防止重复动作

#### 事件`event` 与 `emit`

`event` 是合约发出的“消息日志”，
`emit` 是“发送”这条消息的动作。

==event== 是 Solidity 里一种**日志机制**。  
它不会改变区块链状态（不消耗额外 gas 来存储状态），  
但会把信息写入**交易日志（transaction log）**中，  
方便在区块链浏览器或前端 DApp 中**监听和显示操作记录**。


例如eg

```
event Voted(address indexed voter, string candidate);
```

==emit==：用于触发事件

```
emit Voted(msg.sender, _name);
```

这行代码不会改变合约状态，但会在区块链上留下可查记录：
	Voted(voter=0x123..., candidate="Alice")


**为什么使用 `indexed`？**

	你会注意到每个事件都包含 `address indexed userAddress`。那么 `indexed` 是怎么回事呢？
	
	当你将一个参数标记为 `indexed` 时，你使它变得**可搜索**。这意味着你可以在前端根据该特定值筛选日志。
	
	例如，如果你只想在 Alice 的个人资料页面上显示她的事件，你的前端可以查询日志，并只拉取那些 `userAddress == Alice` 的事件。
	
	如果没有 `indexed`，你就必须扫描每一个事件日志并手动检查——效率低下且速度缓慢。
	
	有了 `indexed`，它就变得优化和可扩展——这正是一个 dApp 所需要的。
	
	需要注意一点：在一个事件中，你最多只能索引**三个**参数。所以要明智地使用它们。

发出事件是智能合约暴露数据<mark style="background: #FFB86CA6;">最节省 Gas</mark> 的方式之一。这就是为什么我们经常将它们用于前端更新、分析以及任何不需要永久存储在状态中的事情。

#### keccak256(bytes(...))

作用：判断字符串是否相同。

why？
	Solidity 没有直接的字符串比较运算（`==` 只适用于固定长度类型）。
			↓
	要**把字符串转成哈希再比较**。

what？
	`keccak256()` 是 Solidity 内置的**哈希函数**==>对应以太坊常用的 **SHA3-256** 哈希算法

eg.
`bytes32 hash = keccak256(abi.encodePacked("Alice"));`

这会输出一个固定长度的 32 字节哈希值。



- [ ] 待办：set、get函数是否能直接生成

## Day 4

### 代码

immutable去修饰不可变量
去掉冗余的条件判断
增加事件 event
增加权限控制：仅限合约拥有者

为何把if(amount>highestBid){的条件去掉？
	**提前用 `require` 确认出价一定比最高价高**，于是这个 `if` 就成了多余的分支判断。


### 知识点

注意！
Solidity 编译器无法识别中文字符。在 Solidity 中，如果你要使用非 ASCII 字符（如中文），你需要明确声明字符串为 ==Unicode== 字符串。

- [ ] 待办：编辑器报错总是报到下一行
#### 全局变量msg.sender

`msg.sender` 是一个非常核心的全局变量，表示 **==当前调用合约的账户地址==**，数据类型是address。
	- 如果是**外部账户**（用户）发起调用，就是该用户的钱包地址。
	- 如果是**合约**发起调用，就是那个合约的地址。

|变量|含义|常见用途|
|---|---|---|
|`msg.sender`|当前调用者地址|验证权限（如仅限合约拥有者操作）|
|`msg.value`|发送的 ETH 数量（单位：wei）|用于接收付款或判断转账金额|
|`msg.data`|调用的原始输入数据|低级函数调用或代理合约中使用|

**常见误区**：
- `msg.sender` **不是**固定的调用者，它随着每次调用变化。
    
- 如果合约 A 调用合约 B，那么在 B 中 `msg.sender` 是 **A 的地址**，而不是原始用户。  
    这种情况在 **代理合约 / DeFi 合约** 中尤为重要。

#### 构造函数 construct
运行一次并设置合约的初始状态

**语法：**
`constructor([参数]) [可见性修饰符] {     // 初始化逻辑 }`

|特点|说明|
|---|---|
|执行时机|只在部署时执行一次|
|是否能再次调用|❌ 不能。部署后就无法再调用|
|是否必须写|否，可省略|
|作用|初始化合约状态，比如设置所有者、初始数值等|
注意：Solidity 0.7.0 之后 **不能再写 `public` 或 `internal`**，只需写 `constructor()`。

- **不能被继承的构造函数**  
    如果父合约<mark style="background: #FFF3A3A6;">没有显式</mark>构造函数，子合约会自动调用默认构造函数。
    
- **继承时要显式调用父构造函数**
	有多的内容自行添加

## Day 5
### 代码



### 知识点
#### 修饰符modifier

modifier 修饰符名字(){
	require（条件）；
	-; //占位符
}

## Day 6
### 代码
用户要分为注册和未注册？
require(members[msg.sender],"Only members can perform this action");]);
不可以直接这样吗？是不是更简单一些？

address(0)和0的区别
### 知识点
#### payable

## Day 7
### 代码
### 知识点
#### 嵌套映射

#### wei
ETH 的最小单位

- [x] 完成基础的6、7；将知识点标题写好（后面再结合代码详细）；将代码问题准备好；
- [x] 再pr上去
- [ ] 最后有时间再优化