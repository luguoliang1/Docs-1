# Getting Started
KeyShard custody service provides the advanced technology and the architecture to secure crypto assets. 

KeyShard provides the framework to create wallet, which is controlled by two or multiple participants. Access control is managed by the Quorum Policy for all of the Participants. The underlying framework is based on threshold signature, which is a specific protocol of secure multi-party computation.

# Pre-requisites
## ubuntu
* gcc版本要求大于等于5.4.0
* jdk版本要求大于等于1.8
* 下载lib库到运行的机器，配置LD_LIBRARY_PATH到本地lib目录
* 配置运行机器/etc/hosts，添加
192.168.16.100       testks.platon.network

## centos
* 

# Java
## Package Details

1. Get KeyShard SDK package (Please contact us from the homepage);
2. Decompress the package.

The Java package contains the following:

- bin - libraries that are dependencies for the Java files.
- docs - HTML descriptions of the APIs.
- jar - contains the Keyshard SDK Java code.
- sample - sample code.

## Usage
### Initialize Keyshard SDK
The main entry to the KeyShard SDK for Java is the class com.platon.keyshard.sdk.keyshardSdk.

Use its instance function to get a singleton instance to work with:

```java
public static keyshardSdk getInstance()
```

Returns:
- The instance of the SDK

Example:
```java
keyshardSdk keyshard = keyshardSdk.getInstance();
```

Before using the KeyShard SDK you must initialize it with the following command:

```java
keyshard.init(SERVER_URL, STORAGE_URL);
```

Replace SERVER_URL with the URL of the keyshard server.
Replace STORAGE_URL with the path of private key share will be stored by client

You must check the return status to determine if this initialized successfully.

### Init Wallet
A KeyShard participant can be a participant of several wallets. When creating a new wallet on the KeyShard server, the relevant participants are added to the wallet according to policy.

```java
class Participant {
	private String email;
	private String companyName;
	private String name;
}

enum Policy {
	NONE("NONE"),
	KEYSHARD22("2-2"),
	KEYSHARD23("2-3")
}

public int intWallet(Participant participant, Policy policy, String walletName, String coinType)
```

Parameters:

- participant - contains the participant info.
- policy - policy which controls members access wallet
- coinType - type of crypto currency

Returns:
- The id of wallet

For example:

```java
int walletId = keyshard.intWallet(participant, KEYSHARD22, "platon", "Energon");
```

### Join Wallet
When one participant requests to generate wallet key-pair,  the relevant participants must be added to the wallet ,and KeyShard server will notify  relevant participants to approve operations.
Once all relevant participants approve the request, KeyShard will start the process of creating a new wallet key-pair,(each participant has its own private key share,but public key is same )

To generate wallet key-pair, keyshardSDK provide the joinWallet method:

```java
public void joinWallet(int walletId, KeyshardSdk.CompletionListener listener)
```

Parameters:

- walletId - wallet ID to load
- listener - handle response msg, when joinWallet success ,  publickey and private key share will stored in STORAGE_URL

For example:

```java
Keyshard.instance().joinWallet(walletId, status -> {
// check status
});
```

After the wallet is created, the application can get wallet information:

```java
public void getWallet(String walletId,
KeyshardSDK.GetWalletlistener listener)
```

Parameters:

- walletId - wallet ID to load
- listener - completion listener

### Wallet transfer
KeyShard provides a multi-participant (quorum) transfering API, used to sign transaction.
To enhance security, transfer transactions are verified on the participant's device, If verification fails, then the SDK returns an error and the transfer is halted.


To transfer wallet balance , keyshardSDK provide the applyTransfer method:
```java
public void applyTransfer(int walletId, String toAddress, BigInteger amount,
keyshardSdk.CompletionListener listener)
```

Parameters:

- walletId - wallet ID to load
- toAddress - transfer to address
- amount   -  transfer amount
- listener - completion listener

For example:

```java
keyshard.applyTransfer(walletId, toAddress, amount, status -> {
// check status
});
```

After the minimum number of participants (as configured in the wallet policy) approve the transaction, threshold signing is ready for execution.