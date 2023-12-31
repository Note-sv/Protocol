# NOTE Protocol

The NOTE protocol enables blockchain systems based on the Bitcoin UTXO model to create and manage digital assets. This protocol supports secure encrypted data storage and communication, and is also applicable to public and transparent data storage, including the management of ownership rights for digital assets such as Tokens and NFTs.

This protocol is grounded in the BIP32 standard to determine the generation rules for Decentralized Identities (DID).

For encrypted data, the protocol employs DID rules. Each record is encrypted and decrypted with unpredictable random public and private keys, using the Electrum BIE1 ECIES algorithm for data encryption.

The protocol specifies the data storage format and defines the rules for data creation, deletion, modification, retrieval, and sharing.

Information security does not rely on third-party trust; only the holders of mnemonic phrases can access the data, ensuring both data and storage security.

Utilizing on-chain smart contracts for Bitcoin consensus-level asset verification, combined with off-chain asset indexers, the protocol efficiently interprets on-chain assets and provides Merkle proofs for these assets.

This is a second-layer solution built upon the unshakeable Bitcoin protocol and its underlying blockchain consensus rules. The protocol also lays the foundational application requirements for Layer 2 networks.

[Protocol English version](./NOTE-Protocol-V2-English.md)

[Protocol Chinese version](./NOTE-Protocol-V2-Chinese.md)

If there are any language errors or logic problems, please let us know.


---
NOTE协议使基于比特币UTXO模型的区块链能够创建及管理数字资产。该协议支持强加密数据存储与通信，亦适用于公开透明的数据存储，包括Token与NFT等数字资产的所有权管理。

本协议基于BIP32标准，确定去中心化身份（DID）的生成规则。

针对加密数据，本协议采用DID规则，对每条记录以难以预测的随机公私钥加密解密，并使用Electrum BIE1 ECIES算法进行数据加密。

协议明确数据存储格式，并定义了数据的增删改查及分享规则。

信息安全无须依赖第三方信任，仅助记词持有者可访问数据，确保数据安全与存储安全。

利用链上智能合约进行比特币共识级别的资产校验，结合链下资产索引器，高效解读链上资产并提供资产的默克尔证明。

此为一种第二层解决方案，基于牢不可破的比特币协议及其底层区块链共识规则。本协议也为Layer 2网络奠定应用基础需求。


[中文版协议](./NOTE-Protocol-V2-Chinese.md)

[英文版协议](./NOTE-Protocol-V2-English.md)

如果有任何语言错误或者逻辑问题，请告知


