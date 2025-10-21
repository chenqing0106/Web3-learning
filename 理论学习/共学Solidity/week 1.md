## 总览
👭第一周学习委员，开始接单啦～
https://www.notion.so/0xherstory/291064212688800cbc8df13ea1ec5502?source=copy_link

✨助教：带大家认识我们的四位助教！@iceฉันคิดถึง @中羊 @segment7 @Rebecca🌞 

🪐导师：@Bala🌍  + Sneha（on TG）

————
🙋遇到问题怎么办？

1⃣️先问问AI：国内外大模型随心选！
2⃣️聪明的你解决了！可以随手把解决方案共享到https://www.notion.so/0xherstory/2910642126888046a897d75705d86a58?v=291064212688808e81d0000c1e304f45&source=copy_link

3⃣️遇到阻力，先喝口水、动一动，休息一下！组织好语言+屏幕截图等有效信息，发到HerSolidity大群里 或 TG群。

祝大家探索愉快！

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

- **`public`**：公有的，表示任何外部或内部的用户都可以访问或调用。对于函数，`public` 表示可以通过交易或者其他合约调用该函数；对于变量，`public` 表示可以从合约外部读取该变量的值（Solidity 会自动生成 getter 函数）。
    
- **`private`**：私有的，表示该函数或变量只能在当前合约内部访问。即使是继承的子合约也不能访问该函数或变量。
    
- **`internal`**：内部的，表示该函数或变量只能在当前合约或其子合约中访问。这是一个常用的修饰符，用于控制继承和子合约的访问权限。
    
- **`external`**：外部的，表示该函数只能通过合约外部调用。通常用于接口函数或其他合约的调用。


## Day 2


需求：
![[Pasted image 20251021162016.png]]

约定俗称的命名：
局部变量和私有函数命名前面加_

**存储类型**：
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

疑问：为何会出现infinite gas
transact和call的区别是什么？
gas是如何计算的？
#### gas的计算



