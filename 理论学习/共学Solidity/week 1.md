## 总览
👭第一周学习委员，开始接单啦～
https://www.notion.so/0xherstory/291064212688800cbc8df13ea1ec5502?source=copy_link

✨助教：带大家认识我们的四位助教！@iceฉันคิดถึง @中羊 @segment7 @Rebecca🌞 

🪐导师：@Bala🌍  + Sneha（on TG）

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

`require(condition, "Error message");`

- `condition`：布尔值表达式，若为 `false`，则会抛出错误并回滚。
    
- `"Error message"`：可选的错误消息，用于描述失败的原因。
    

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

#### 数据类型
除了`uint256`,还有`int`、`bool`、`address`、`string`等类型

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

#### `event` 与 `emit`

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