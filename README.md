
智能合约笔记
===========


# 1.合约


## 合约修饰器
abstract 标志抽象合约合约，抽象合约是指至少有一个函数没有被实现，需要派生合约实现

interface   不能实现任何函数
abstract 至少有一个函数没被实现
contract 不带修饰的合约，则所有函数必需要实现

## 合约地址
合约的地址默认是根据创建合约的地址和每次创建合约交易时的计数器(nonce)来计算,通过加盐可以使合约的地址用盐来计算，可以保证地址可以一致（比如主链和测试链上合约地址相同）
ContractD d = new ContractD(4); /
ContractD d = new ContractD{salt: salt}(arg);

## 合约的调用方式
参考文章：https://blog.csdn.net/TurkeyCock/article/details/83826531  
                         调用
```
User  ----> Contract A  ------> Contract B
       msg1   dataA      msg2    dataB
```
假设如下的调用链：用户User,执行合约A的方法，该方法又用如下3种方式调用了合约B的方法  

- call普通调用  
    msg1.sender是User  
    dataA 不修改  
    msg2.sender是A  
    dataB 修改  

    上下文是B，即消耗B的balance、存储空间  
    A与B的关系：是相互独立的两个完整合约

- delegatecall 代理调用  
    msg1.sender是User  
    dataA 修改  
    msg2.sender是User  
    dataB 不修改  

    上下文是A，即消耗A的balance、存储空间  
    A与B的关系：A是主合约，B是代理合约，只是提供了业务逻辑函数，不提供存储、余额等上下文，B常为库合约，提供库方法

- callcode 旧版的代理调用,不推荐使用  
    msg1.sender是User  
    dataA 修改  
    msg2.sender是A  
    dataB 不修改  

    上下文是A，即消耗A的balance、存储空间  
    A与B的关系：A是主合约，B是代理合约，只是提供了业务逻辑函数，不提供存储、余额等上下文，B常为库合约，提供库方法

- staticcall静态调用（不能写)  
不涉及两个合约的调用，只是编译时的术语，在编译view和pure方法后的得到调用指令，表示该调用不能写数据。  

# 2.状态变量

状态变量
内部访问时是以变量方式访问
外部访问时是通过getter访问，所以是以函数访问

Constant    编译时确定值，不可被修改
Immutable   部署时确定值，不可被修改


# 3.函数

## 函数可见性
内部调用不会产生消息调用
外部调用会产生消息调用

public：可以外部调用或内部调用
external：  只能外部调用函数，不能在内部调用（例外是可以通过this.method调用）
internal：只能内部调用，不可以使用this调用
private：只能在内部调用，并且不能被子合约调用




状态变量，可见性标识符的定义位置，在类型后面，
函数，可见性标识符的定义位置，在参数列表和返回关键字中间。


## 函数修改器
modifier

## 函数修饰器
view    只读函数，可以读状态变量，但不可以修改状态变量，只能调用被view或pure修饰的函数
pure    纯函数，不读取也不修改状态
virtual 标志父合约定义的函数可以被子合约重载
override    标志子合约重载父合约的方法
payable
anonymous
indexed

## 特殊函数
constructor 构造函数
receive 接收以太函数，使合约可以接收以太币
fallback 回退函数，在没有定义receive时，使合约可以接收以太币；调用合约没有定义的方法时，默认调用的函数
assembly 内联汇编


# 异常处理
Assert, Require, Revert



# 4.术语表 
## 全局变量
abi.decode(bytes memory encodedData, (...)) returns (...): ABI-decodes the provided data. The types are given in parentheses as second argument. Example: (uint a, uint[2] memory b, bytes memory c) = abi.decode(data, (uint, uint[2], bytes))
abi.encode(...) returns (bytes memory): ABI-encodes the given arguments
abi.encodePacked(...) returns (bytes memory): Performs packed encoding of the given arguments. Note that this encoding can be ambiguous!
abi.encodeWithSelector(bytes4 selector, ...) returns (bytes memory): ABI-encodes the given arguments starting from the second and prepends the given four-byte selector
abi.encodeWithSignature(string memory signature, ...) returns (bytes memory): Equivalent to abi.encodeWithSelector(bytes4(keccak256(bytes(signature)), ...)`
block.chainid (uint): current chain id
block.coinbase (address payable): current block miner’s address
block.difficulty (uint): current block difficulty
block.gaslimit (uint): current block gaslimit
block.number (uint): current block number
block.timestamp (uint): current block timestamp
gasleft() returns (uint256): remaining gas
msg.data (bytes): complete calldata
msg.sender (address): sender of the message (current call)
msg.value (uint): number of wei sent with the message
tx.gasprice (uint): gas price of the transaction
tx.origin (address): sender of the transaction (full call chain)
assert(bool condition): abort execution and revert state changes if condition is false (use for internal error)
require(bool condition): abort execution and revert state changes if condition is false (use for malformed input or error in external component)
require(bool condition, string memory message): abort execution and revert state changes if condition is false (use for malformed input or error in external component). Also provide error message.
revert(): abort execution and revert state changes
revert(string memory message): abort execution and revert state changes providing an explanatory string
blockhash(uint blockNumber) returns (bytes32): hash of the given block - only works for 256 most recent blocks
keccak256(bytes memory) returns (bytes32): compute the Keccak-256 hash of the input
sha256(bytes memory) returns (bytes32): compute the SHA-256 hash of the input
ripemd160(bytes memory) returns (bytes20): compute the RIPEMD-160 hash of the input
ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address): recover address associated with the public key from elliptic curve signature, return zero on error
addmod(uint x, uint y, uint k) returns (uint): compute (x + y) % k where the addition is performed with arbitrary precision and does not wrap around at 2**256. Assert that k != 0 starting from version 0.5.0.
mulmod(uint x, uint y, uint k) returns (uint): compute (x * y) % k where the multiplication is performed with arbitrary precision and does not wrap around at 2**256. Assert that k != 0 starting from version 0.5.0.
this (current contract’s type): the current contract, explicitly convertible to address or address payable
super: the contract one level higher in the inheritance hierarchy
selfdestruct(address payable recipient): destroy the current contract, sending its funds to the given address
<address>.balance (uint256): balance of the 地址类型 Address in Wei
<address>.code (bytes memory): code at the 地址类型 Address (can be empty)
<address>.codehash (bytes32): the codehash of the 地址类型 Address
<address payable>.send(uint256 amount) returns (bool): send given amount of Wei to 地址类型 Address, returns false on failure
<address payable>.transfer(uint256 amount): send given amount of Wei to 地址类型 Address, throws on failure
type(C).name (string): the name of the contract
type(C).creationCode (bytes memory): creation bytecode of the given contract, see Type Information.
type(C).runtimeCode (bytes memory): runtime bytecode of the given contract, see Type Information.
type(I).interfaceId (bytes4): value containing the EIP-165 interface identifier of the given interface, see Type Information.
type(T).min (T): the minimum value representable by the integer type T, see Type Information.
type(T).max (T): the maximum value representable by the integer type T, see Type Information.

## 函数可见性

public: visible externally and internally (creates a getter function for storage/state variables)
private: only visible in the current contract
external: only visible externally (only for functions) - i.e. can only be message-called (via this.func)
internal: only visible internally

## 修饰符
pure for functions: Disallows modification or access of state.
view for functions: Disallows modification of state.
payable for functions: Allows them to receive Ether together with a call.
constant for state variables: Disallows assignment (except initialisation), does not occupy storage slot.
immutable for state variables: Allows exactly one assignment at construction time and is constant afterwards. Is stored in code.
anonymous for events: Does not store event signature as topic.
indexed for event parameters: Stores the parameter as topic.
virtual for functions and modifiers: Allows the function’s or modifier’s behaviour to be changed in derived contracts.
override: States that this function, modifier or public state variable changes the behaviour of a function or modifier in a base contract.

## 保留关键字
These keywords are reserved in Solidity. They might become part of the syntax in the future:

after, alias, apply, auto, case, copyof, default, define, final, immutable, implements, in, inline, let, macro, match, mutable, null, of, partial, promise, reference, relocatable, sealed, sizeof, static, supports, switch, typedef, typeof, unchecked.