# NOTE Protocol User White Paper

>Copyright notice and instructions

>ChainBow Co. Ltd. And the author Long LI reserve all rights. It is also publicly licensed for non-commercial applications based on the BitcoinSV blockchain network. No liability shall be assumed for any errors or inaccuracies that may arise in this document.

>The content of this article is the user agreement White Paper. Users of NoteSV software can use this protocol to recover all their personal data with their own mnemonics.

>For commercial use of this agreement, please contact support@chainbow.io for a paid licensing and a more detailed technical white paper.

## 1. Introduction

We propose a new protocol for private data storage in distributed peer-to-peer Internet, which uses the underlying blockchain to store the data in a directed graph structure. The protocol uses BIP32 standard to determine the generating rules of the private key, uses the Electrum BIE1 ECIES algorithm to encrypt the data, specifies the storage format of the data, and realizes the rules of adding, deleting, changing and sharing the data.

According to the NOTE protocol, your information security does not need to trust any third party, to achieve data security and storage security, only mnemonic holders can access. And the data is not stored on a specified third party server or cloud service.

This is a layer-2 solution, built on the set-in-stone Bitcoin protocol and the consensus rule of the blockchain.

The protocol supports high-concurrency chaining of data from the same mnemonic account, encryption and decryption of each record using unpredictable random public-private keys, sharing of encrypted data between multiple users, and other features.

## 2. Prepare the knowledge
### 2.1 BIP32

[BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) defined an algorithm of private key derived from mnemonic. The corresponding public key and public key Hash (i.e. address) can be obtained by the private key.

### 2.2 Electrum BIE1 ECIES

[Electrum BIE1 ECIES](https://github.com/benw46/BIE1) defined how to encrypt and decrypt data using the elliptic curve algorithm.

### 2.3 Bitcom

[Bitcom](https://bitcom.bitdb.network/#/?id=bitcom) protocol defined each account namespace.

## 3. NOTE Protocol
### 3.1 Derived index and private key derived path

```plain
m/purpose'/coin_type'/account'/target/quotient/remainder
```
define:

```plain
purpose = 44
coin_type=236
account=0
target=2
```
A 64 bit number is randomly generated as a derived index, and quotient and remainder are calculated. The index is IEEE754 compliant and requires between 1 and 2 ^ 53-1.

```javascript
  const Hardened = 0x80000000
  const quotient = Math.floor(index / Hardened)
  const remainder = index % Hardened
```
The full derivation path is
```javascript
  const extPath = `m/44'/236'/0'/2/${quotient}/${remainder}`
```
### 3.2 Root private key and root address

The root private key is derived based on the following path

```javascript
  const extPath = `m/44'/236'/0'`
```
### 3.3 Storage format

The data is stored as OP_RETURN value and is divided into four parts:

```plain
OP_FALSE OP_RETURN Root address, derived index, encrypted data, signature
```
And 

1. Root address: root private key generation
2. Derived index: a randomly generated 64-bit number
3. Encrypts data: A key pair created using a derived index, encrypts data using the public key in the pair
4. Signature: the encrypted data is signed using the root private key

### 3.4 Signature

The signature uses the root private key to sign the SHA256 HASH of the encrypted data. The root address public key can be made public, and anyone can verify that the data has not been forged with the root address public key. For the purpose of determining power.

```plain
sign（sha256( encrypedNote ))
```
### 3.5 Data Format

User data can be used in any format. The data is encrypted using the Electrum BIE1 ECIES encryption algorithm.

The following JSON format definition belongs to NoteSV, the secure note and password management software. Other software using the NOTE protocol can be defined

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


* id: main key
* tpl: template
* ttl: title
* mem: content
* fid: user name
* pwd: user password
* url: link of website
* otp: one-time password key
* tags: tags/labels
* files: attached files
* del: delete flag
* ptx: parent Tx ID
* tms: Unix timestamp

## 4. Create, update, and delete data

The ID of the data creation is in the form of the current Unix timestamp plus a random number. This allows you to sort ids chronologically and avoid multiple apps that store data concurrently with the account.

Data updates do not change the data id, only the other fields.

The Data ID is not changed when the data is deleted, but the DEL field is set to true.

## 5. Data fetching

The root address in the data store is entered into the BITCOM protocol, and all transactions are retrieved using a Bitcom-enabled bitcoin data service provider.

## 6. Data Sharing

Before sharing the data, you need to get the other party’s root public key. The storage format is

```plain
OP_FALSE OP_RETURN Root address, derived index, encrypted data, signature
```

1. Root address: get the root address of the other party through the public key
2. Derived index: set to 0
3. Encrypts data: encrypts data using the other party’s root public key
4. Signature: the encrypted data is signed using your own root private key

## 7. Data security
### 7.1 Signature

All data can be verified using the root address public key to determine whether the data was created by the person who owns the root address or by the sharers. The reason for this check is that the person who created the data knew the public key of the encrypted data and forged a piece of data, but did not know the private key of the root address and could not forge the signature.
