## Day 15
### 代码

- [x] uint的大小都什么情况用？以便节省资源
	[[#uint大小]]

  - [x] 为什么要专门搞一个proposalVoterCount 数组？不能从proposals 中提取吗(eg 搞一个get函数？还是说get函数消耗的gas太高了)    proposals[proposalId].voteCount++;     proposalVoterCount[proposalId]++;
	  访问 proposals 内部结构，要用额外的数组！！！
	  gas 可能更高
	  所以此处用mapping (uint8=>uint32)public proposalVoterCount;进行优化

- [x] 代码理解：
 uint256 voterData=voterRegistry[msg.sender];
uint256 mask=1<<proposalId;   require((voterData&mask)== 0,"Already voted"); voterRegistry[msg.sender]=voterData | mask;
	1<<表示左移动移位；
	       如果 `proposalId = 2`；
	 mask = 1 << 2 = 000...0100 (第3位为1);
	 假设 voter 当前的投票状态：`voterData = 000...0000 (尚未投票)`
	投完票后：
	`voterData | mask = 000...0100`
	这样只用一个 `uint256` 就能记录 **最多 256 个提案的投票状态**，无需存数组（极大节省 gas）。
  
### 知识点
#### gas优化

|优化点|原因|示例|
|---|---|---|
|✅ 用 `bytes` 代替 `string`|动态字符串操作贵|`bytes32 name;`|
|✅ 合理选 `uint` 位数|减少存储空间|`uint8 status; uint16 count;`|
|✅ 用 `mapping` 代替 `array`|无边界检查，更直接|`mapping(uint=>Proposal)`|
|✅ 用位运算管理状态|一次性存多个布尔状态|`1<<proposalId` 掩码法|
|❌ 不滥用小 uint|单独占 slot 不省 gas|`uint8 a; // 无意义`|


#### uint大小
| 类型                  | 占位字节     | 取值范围        | 适用场景                |
| ------------------- | -------- | ----------- | ------------------- |
| `uint8`             | 1字节（8位）  | 0～255       | 计数（少量数据，如小型枚举、flag） |
| `uint16`            | 2字节（16位） | 0～65535     | 小范围数值               |
| `uint32` / `uint64` | 4～8字节    | 中等数值        |                     |
| `uint128`           | 16字节     | 较大范围，但仍节省空间 |                     |
| `uint256`           | 32字节（默认） | 最大范围        | ==最常用、安全==（溢出少）     |
局部变量、状态变量如果能确定不会超过某个范围，可以用更小位数（如 `uint8`、`uint16`），但**必须同 slot 打包**才节省 gas。

#### slot 打包
以太坊虚拟机（EVM）把 **所有状态变量** 存在一种叫做 **storage** 的地方。  
storage 被划分为很多个固定大小的“格子”，==每个格子就是 **一个 slot（存储槽）**==，每个 slot 大小是：

> ==32 字节==（= 256 bit）

每个 slot 编号从 0 开始。  
默认情况下，每个状态变量独占一个 slot。

如果多个 **连续声明的变量** 的总大小 ≤ 32 字节，  
编译器会自动把它们 **打包到同一个 slot 中**，  
从而节省空间和 gas。

 ``` solidity
 contract Example1 {
    uint128 a; // 16字节
    uint128 b; // 16字节
    // ✅ a+b = 32字节，正好打包进1个slot
}

contract Example2 {
    uint128 a;
    uint256 b;
    // ❌ b是256位，单独占一个slot
}
 ```
#### bitmask
| 操作   | 符号   | 作用              | 举例                   |
| ---- | ---- | --------------- | -------------------- |
| 按位与  | `&`  | 保留两者都为 1 的位     | `1101 & 1011 = 1001` |
| 按位或  | `    | `               | 有一个为 1 即为 1          |
| 按位异或 | `^`  | 不同为 1，相同为 0     | `1100 ^ 1010 = 0110` |
| 按位取反 | `~`  | 0→1，1→0         | `~0001 = 1110...`    |
| 左移   | `<<` | 向左移动 n 位（乘以 2ⁿ） | `1 << 3 = 1000`      |
| 右移   | `>>` | 向右移动 n 位（除以 2ⁿ） | `1000 >> 3 = 1`      |
