
##
本项目专用于以太坊vm学习.
那么以太坊虚拟机是什么,为什么要有虚拟机,他又干了什么,要怎样回答这些问题呢?

作为一个以太坊开发者,我们有过使用solidity编写合约,编译后部署到链上的经验.
大概知道在部署合约的时候可以使用loadScript的方式,也可以使用编译后得到的字节码直接部署.
但是,这个部署的具体过程又是怎么样的呢?

以太坊EVM是一个图灵完备的虚拟机,他将transaction解析成合约对象,并执行.

我们编写一个简单的合约:
pragma solidity ^0.4.16;

contract C {

    uint256 a;
    function C() {
        a = 1;
    }

}

运行以下命令:
```cmd
fukundeMacBook-Pro:ethereum-vm fukun$ solc --bin --asm solc/t1.sol

======= solc/t1.sol:C =======
EVM assembly:
    /* "solc/t1.sol":26:97  contract C {... */
  mstore(0x40, 0x60)
    /* "solc/t1.sol":59:94  function C() {... */
  jumpi(tag_1, iszero(callvalue))
  0x0
  dup1
  revert
tag_1:
tag_2:
    /* "solc/t1.sol":86:87  1 */
  0x1
    /* "solc/t1.sol":82:83  a */
  0x0
    /* "solc/t1.sol":82:87  a = 1 */
  dup2
  swap1
  sstore
  pop
    /* "solc/t1.sol":59:94  function C() {... */
tag_3:
    /* "solc/t1.sol":26:97  contract C {... */
tag_4:
  dataSize(sub_0)
  dup1
  dataOffset(sub_0)
  0x0
  codecopy
  0x0
  return
stop

sub_0: assembly {
        /* "solc/t1.sol":26:97  contract C {... */
      mstore(0x40, 0x60)
    tag_1:
      0x0
      dup1
      revert

    auxdata: 0xa165627a7a7230582014f79780d5cd854cec5b2c2f170419d321d599b333c04305860211d979e9a9860029
}
Binary:
60606040523415600e57600080fd5b5b60016000819055505b5b60368060266000396000f30060606040525b600080fd00a165627a7a7230582014f79780d5cd854cec5b2c2f170419d321d599b333c04305860211d979e9a9860029

```

从上述编译结果中,我们可以看到一些类汇编命令,以及一些tag和注释,那这些命令是什么,干什么用的呢?
这些命令(tag除外)都可以从go-ethereum/core/vm/jump_table.go中找到.

Binary 60606040523415600e57600080...是我们得到的字节码


