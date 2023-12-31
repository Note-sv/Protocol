# NOTE协议用户白皮书

>版权声明及使用说明

>株式会社ChainBow和作者李龙保留所有权利。本文件公开授权给基于BitcoinSV区块链网络的非商业应用使用。对于本文件中可能出现的任何错误或不准确之处，我们不承担任何责任。

>本文档是一份用户协议白皮书。使用NoteSV软件的用户，可以基于此协议通过自己的助记词恢复所有的个人数据。

>如需商业使用此协议，请联系 support@chainbow.io，有偿获取授权以及更详细的技术白皮书。

## 1. 简介

我们提出了一种新的NOTE协议，用于在分布式点对点互联网中保护隐私数据。该协议使用底层区块链将数据存储在有向图结构中。该协议利用BIP32标准确定私钥的生成规则，使用Electrum BIE1 ECIES算法对数据进行加密，指定了数据的存储格式，并实现了数据的增删改查和分享规则。

根据NOTE协议，您的信息安全不需要信任任何第三方。数据安全和存储安全得以实现，只有助记词持有人可以访问数据。数据不会保存在特定的第三方服务器或云服务上。

这是一个第二层解决方案，它建立在不可更改的Bitcoin协议和底层区块链的共识规则之上。

该协议支持同一个助记词账号的数据高并发上链，每一条记录使用无法预测的随机公私钥进行加密解密，支持多用户之间的加密数据分享等特性。

## 2. 准备知识
### 2.1 BIP32

[BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)定义了一套通过助记词派生私钥的算法。通过私钥可以获取对应的公钥和公钥哈希（即地址）。

### 2.2 Electrum BIE1 ECIES

[Electrum BIE1 ECIES](https://github.com/benw46/BIE1)定义了如何使用椭圆曲线算法加密和解密数据。

### 2.3 Bitcom

[Bitcom](https://bitcom.planaria.network/#/?id=bitcom)协议定义了每个账号的命名空间。

## 3. NOTE协议
### 3.1 派生索引和私钥派生路径

私钥派生路径定义如下：


```plain
m/purpose'/coin_type'/account'/target/quotient/remainder
```
其中

```plain
purpose = 44
coin_type=236
account=0
target=2
```
随机生成一个64位数字作为派生索引，计算商和余数。索引需符合IEEE754标准，值应在1和2^53-1之间。

```javascript
  const Hardened = 0x80000000
  const quotient = Math.floor(index / Hardened)
  const remainder = index % Hardened
```
完整的派生路径为：

```javascript
  const extPath = `m/44'/236'/0'/2/${quotient}/${remainder}`
```
### 3.2 根私钥和根地址

根私钥基于以下路径派生

```javascript
  const extPath = `m/44'/236'/0'`
```
### 3.3 存储格式

数据存储在OP_RETURN后面，分为四个部分：


```plain
OP_FALSE OP_RETURN 根地址 派生索引 加密数据 签名
```
其中

1. 根地址：根私钥生成
2. 派生索引：随机生成的64Bit数字
3. 加密数据：使用派生索引创建的密钥对，使用其中的公钥加密数据
4. 签名：使用根私钥对加密后的数据进行签名
   
### 3.4 签名

签名使用根私钥对加密数据的SHA256哈希进行签名。根地址公钥可以公开，任何人都可以用根地址公钥校验数据是否被伪造，以达到确权的目的。

```plain
sign（sha256( encrypedNote ))
```
### 3.5 数据格式

用户数据可以使用任意格式。数据经过使用Electrum BIE1 ECIES加密算法进行加密。

以下JSON格式定义属于NoteSV，这是一个密码管理软件。其他使用NOTE协议的软件可以自行定义：

```plain
{
  id:
  tpl:
  ttl:
  mem:
  fid:
  pwd:
  url:
  otp:
  ptx:
  tags: []
  files:[],
  tms
}
```
其中

* id: 数据的主键
* tpl: 内容模版
* ttl: 标题
* mem: 正文
* fid: 用户名
* pwd: 用户密码
* url: 网站链接
* otp: 一次性密码key
* tags: 内容标签
* files: 文件数组
* del: 删除标志
* ptx: 父内容的交易id
* tms: Unix时间戳

## 4. 数据的创建更新与删除

数据创建时的id使用当前UNIX时间戳外加随机数的形式。这样做可以按照时间顺序排列id，并且可以避免多个同账号APP并发存储数据时产生冲突。

数据更新时不改变数据id，只改变其他字段。

数据删除时不改变数据id，只是将del字段设置为true。


## 5. 数据检索

数据存储中的根地址输入Bitcom协议，使用支持Bitcom协议的比特币数据服务商，可以检索到所有交易记录。


## 6. 数据分享

数据分享之前，需要获取对方的根公钥。存储格式为：


```plain
OP_FALSE OP_RETURN 根地址 派生索引 加密数据 签名
```
其中

1. 根地址：通过对方根公钥获取对方的根地址
2. 派生索引：设置为0
3. 加密数据：使用对方根公钥加密数据
4. 签名：使用己方根私钥对加密后的数据进行签名

## 7. 数据防伪
### 7.1 签名

所有数据均可使用根地址公钥校验，确定数据是拥有根地址的人创建的，或者由分享方创建。这步校验的理由是，创建数据的人知道了加密数据的公钥，伪造一条数据，但是不知道根地址的私钥，所以无法伪造签名。
