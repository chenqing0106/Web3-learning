哈希指针使用在区块链和merkle tree

1.哈希指针，多存储了哈希值。防篡改
2.merkle tree 。与二叉树的区别是指针是哈希指针。提供merkle proof。
全结点：block header（根hash）+body（具体的交易列表）
轻结点：header
利用proof，向全结点请求需要的hash值，最后得到的根hash看是否对应来判断轻结点是否是该区块链中的交易。从下往上验证o（logn）

问题：如何向全结点请求？

proof of membeship/inclusion 证明交易存在
若证明不存在呢？
”笨方法”：验证整棵树，得到最终交易的列表，发现该交易不在里面-o（n）
“高效方法”：对叶节点（即交易）按照hash从小到大排序（sorted merkle tree），然后如何反证？没听懂-o（logn）

但比特币中不需要反证需求，则没有这种tree

链无环，都能用hash指针代替普通指针
有环出现的问题：循环依赖