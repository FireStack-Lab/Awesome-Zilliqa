# 交易所集成指南



# Token交换时间表和概述



#### 注意

> Zillings（“ZILs”）的临时Zillings（“临时ZILs”）不能提供、出售或转让给美国人（如1933年美国证券法第S条所定义）。请确保在交换临时ZIL时，确认ZIL的每个持有人不是美国人（如1933年美国证券法第S条所定义）。



## 概述

以下流程图说明了交换的Token交换过程。

![token swap](https://zilliqa.github.io/dev-portal/img/token_swap_210119_3.png)

从交易所的角度来看，进一步描述如下：

- 确定进行交换的日期（例如：2019年5月31日）
 - 交换日期前1周，所有**ERC20 ZIL**交易必须冻结
 - 2019年5月31日，将所有**ERC20 ZIL**发送至**ETHEREUM**的`ZilSwap`合同（地址待提供）
 - 一旦超过30个区块，请告知Zilliqa您的交易哈希，并提供**一个**您想在**Zilliqa**区块链上接收本地ZIL的地址。
 - 本地ZIL将存入您选择的地址。
 - 使用**本地ZIL**恢复交易。

## 时间表

### Bootstrap阶段：2019年2月至3月

在此阶段，网络上不处理任何交易。 此时交易所不需要采取任何行动。

### Token交换阶段：2019年4月至6月

交易所应选择此范围内的日期，并至少提前**一个月**通知Zilliqa。 此后，交易所应在指定日期进行**一次**上述一次性交换。

交易所可以支持**ETHEREUM**上的**ERC20 ZIL**交易，直到Token交换日期**前一周**，此时**所有**交易必须冻结，直到交易所在**Zilliqa**网络上收到**本地ZIL**。

此后，交易所应使用[示例程序](https://github.com/Zilliqa/dev-portal/tree/master/examples/exchange)作为指导，仅支持在Zilliqa上的ZIL交易，这并不包括ERC20交易。

### 冻结与销毁: 2019年7月

2019年7月1日，所有送往ZilSwap的ERC20 ZIL将被销毁，所有剩余的ERC20 ZIL（即未开封的ZIL）的转移也会被冻结。 所有剩余的ERC20 ZIL将无法更换。



# 入门



#### 注意

> Zillings（“ZILs”）的临时Zillings（“临时ZILs”）不能提供、出售或转让给美国人（如1933年美国证券法第S条所定义）。 请确保在交换临时ZIL时，确认ZIL的每个持有人不是美国人（如1933年美国证券法第S条所定义）。



虽然可以使用Zilliqa提供的公共节点与区块链进行交互，但我们建议所有想支持主网交易的交易所设置种子节点。本文档将指导您完成启动和运行所需的基本步骤。

## KYC和IP白名单

由于种子节点不直接从查找或分片节点中提取数据，因此Zilliqa必须将交易所列入白名单，以便接收有关区块链及其状态的数据广播。这需要一个静态的公共IP地址，最少有两个可以接收的开放端口（输入和输出）。

此外，由于种子节点提供商将获得奖励，我们要求所有想设置种子节点的交易所和个人通过KYC流程。 您可以通过填写我们的[>>**表单**<<](https://docs.google.com/forms/d/e/1FAIpQLScopeiLXU_10i6OzsZApIDyRYHpw4JqePDDe0Aoa5JIZo1muw/viewform)来开始此流程。请注意，在KYC完成之前，您将无法设置种子节点。

## 最低硬件要求

- x64 Linux OS（首选Ubuntu 16.04）
 - 双核CPU或更高CPU（例如Intel i5处理器）
 - 8GB DDR3 RAM或更好
 - 500GB固态硬盘
 - 100MB/s的上传和下载带宽

## 硬件准备

在开始之前，请选定并记下您的种子节点的通信端口。此步骤非常重要，因为未能提供正确的参数将会导致失败。

### Docker设置

> 注意：如果您使用AWS，则可以使用**ami-0812864268c7448b6**创建一个实例并跳过此步骤。

我们强烈建议使用[Docker](https://docker.com/)来设置种子节点，因为我们提供了经过测试的正式环境镜像供您使用。如果您尚未设置docker，请按照[官方文档](https://docs.docker.com/install/)中的说明进行操作。

设置Docker后，您可以继续下载mainnet的配置tarball：

```sh
# create a directory
$ mkdir my_seed && cd my_seed
$ curl -O https://mainnet-bugis-seedjoin.aws.zilliqa.com/configuration.tar.gz
$ tar -zxvf configuration.tar.gz

# Contents:
#
# launch.sh
# constants.xml
# launch_docker.sh
# dsnodes.xml
# download_and_verify.sh
# fetchHistorical.py
# fetchHistorical.sh
# config.xml
```

一旦成功解压缩了tarball，就要生成一个新的密钥对，如下所示：

```sh
$ ./launch_docker.sh --genkeypair
$ mv mykey.txt verifier.txt
```

### 本地设置

> 注意：这种方法仅在**Ubuntu 16.04**上进行了测试，并涉及编译C ++。我们强烈建议您考虑使用上面提供的Docker镜像。

如果你不能或不想使用Docker，你也可以从源代码构建Zilliqa二进制文件然后运行。

```sh
# clone Zilliqa source files
$ git clone https://github.com/Zilliqa/Zilliqa.git && cd Zilliqa && git checkout
<<commit_sha>> && cd Zilliqa

# install system dependencies
$ sudo apt-get update && sudo apt-get install \
    git \
    libboost-system-dev \
    libboost-filesystem-dev \
    libboost-test-dev \
    libssl-dev \
    libleveldb-dev \
    libjsoncpp-dev \
    libsnapp-dev \
    cmake \
    libmicrohttpd-dev \
    libjsonrpccpp-dev \
    build-essential \
    pkg-config \
    libevent-dev \
    libminiupnpc-dev \
    libprotobuf-dev \
    protobuf-compiler \
    libcurl4-openssl-dev \
    libboost-program-options-dev \
    libssl-dev

# build the binary. this may take awhile.
$ ./build.sh
```

构建应能正常退出。完成后，下载tarball配置，并生成密钥对：

```sh
# make a separate folder for keys and configuration
$ cd ../ && mkdir my_seed && cd my_seed
$ curl -O https://mainnet-bugis-seedjoin.aws.zilliqa.com/configuration.tar.gz
$ tar -zxvf configuration.tar.gz

# generate a keypair
$ ../Zilliqa/build/bin/genkeypair > verifier.txt
```

## 配置节点

在加入主网之前，节点需要进行配置。大多数配置都包含在**constants.xml**中，这个文件在我们解压缩的目录中的**configuration.tar.gz**，需要进行以下更改：

- 将`VERIFIER_PUBKEY`的值更改为`cat verifier.txt | cut -d ' ' -f1`
- 将`SEED_PORT`的值更改为`33133`（默认值），或您的自定义端口（如果有）。如果不选择`33133`，请务必记下后续步骤。

## 加入主网

> 注意：在继续此步骤之前，请确保您已完成KYC。

当完成了初始步骤，加入主网会相对简单些。

```sh
# NOTE: run only ONE of the following.
# for Docker setup
$ ./launch_docker.sh
# for native setup
$ ./launch.sh
```

您将被询问一系列问题。当要求输入您的IP地址和监听端口时，请输入您在提交KYC表单时所填写的值。这非常重要，因为您的节点**不能**与其他任何东西一起使用。

## 下一步

如果您已完成上述步骤，则应该会产生了一个正常运行的种子节点，该节点在`localhost:4201`上公开了RPC API。您可以在`zilliqa-00001-log.txt`查看详细日志。

以下说明将用一组简单的函数来作示例，交易所的开发人员可利用这些函数开始在Zilliqa区块链上实现自定义业务逻辑。您可以在与示例应用程序的[同一目录](https://github.com/Zilliqa/dev-portal/tree/master/examples/exchange)中找到完整源代码。



# 账户管理



#### 注意：

> Zillings（“ZILs”）的临时Zillings（“临时ZILs”）不能提供、出售或转让给美国人（如1933年美国证券法第S条所定义）。 请确保在交换临时ZIL时，确认ZIL的每个持有人不是美国人（如1933年美国证券法第S条所定义）。



本教程中的代码来自[示例应用程序](https://github.com/Zilliqa/dev-portal/blob/master/examples/exchange/src/services/zilliqa.ts)。

## 批量生成账户

交易所的常见任务是安全且可预测地生成大量地址。您可以使用一个或多个[BIP39助记符](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)来完成此操作。

```ts
export class ZilliqaService {
  accounts: string[] = [];
  zil: Zilliqa;

  constructor(api: string, mnemonics: {[mnemonic: string]: number}) {
    const zilliqa = new Zilliqa(api);
    this.zil = zilliqa;

    // you can use one or more mnemonics to manage/generate a large number of accounts
    for (let m in mnemonics) {
      const num = mnemonics[m];
      range(num).forEach(i => {
        const address = this.zil.wallet.addByMnemonic(m, i);
        this.accounts.push(address);
      });
    }
  }

  /* truncated */
}
```

## 导出账户

您可能还希望将密钥对导出为可移植格式，以便在其他位置使用它。我们支持用[Web3密钥存储定义](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition)来实现。

```typescript
import { Zilliqa } from '@zilliqa-js/zilliqa';
import * fs from 'fs';

export class ZilliqaService {
  accounts: string[] = [];
  zil: Zilliqa;

  constructor(api: string, mnemonics: {[mnemonic: string]: number}) {
    const zilliqa = new Zilliqa(api);
    this.zil = zilliqa;

    // you can use one or more mnemonics to manage/generate a large number of accounts
    for (let m in mnemonics) {
      const num = mnemonics[m];
      range(num).forEach(i => {
        const address = this.zil.wallet.addByMnemonic(m, i);
        this.accounts.push(address);
      });
    }
  }

  /* truncated */

  export(address: string) {
    // keep this secret.
    const passphrase = 'something';
    const json = this.zil.wallet.export(address, passphrase);
    // at this point, you should safely write this to disk, or send it to
    // a vault somehwere. the point is to keep it safe.
    fs.writeFile('/path/to/safe/place', json);
  }

  /* truncated */
}
```

现在，您应该能够在指定的路径中找到包含用您的密码加密后的私钥文件。切记要永久保留此文件，特别是您的密码！

## 导入账户

> 注意：以太坊的实现方式与Zilliqa有不同之处。您将无法使用web3解密Zilliqa的keystore文件，反之亦然。

在某些阶段，您可能还需要导入先前导出的keystore文件。为此我们提供了一个方便的方法。

```ts
import pify from 'pify';
import { Zilliqa } from '@zilliqa-js/zilliqa';
import * fs from 'fs';

export class ZilliqaService {
  accounts: string[] = [];
  zil: Zilliqa;

  constructor(api: string, mnemonics: {[mnemonic: string]: number}) {
    const zilliqa = new Zilliqa(api);
    this.zil = zilliqa;

    // you can use one or more mnemonics to manage/generate a large number of accounts
    for (let m in mnemonics) {
      const num = mnemonics[m];
      range(num).forEach(i => {
        const address = this.zil.wallet.addByMnemonic(m, i);
        this.accounts.push(address);
      });
    }
  }

  /* truncated */

  export(address: string) {
    // keep this secret.
    const passphrase = 'something';
    const json = this.zil.wallet.export(address, passphrase);
    // at this point, you should safely write this to disk, or send it to
    // a vault somehwere. the point is to keep it safe.
    fs.writeFile('/path/to/safe/place', json);
  }

  async addKeystoreFile(path: string, passphrase: string) {
    const buf = await pify(fs.readFile)(path);
    const json = buf.toString();
    const address = await this.zil.wallet.addByKeystore(json, passphrase);

    return address;
  }

  /* truncated */
```

这两种方法允许您安全地序列化和反序列化您的帐户。



# 发送交易



#### 注意

> Zillings（“ZILs”）的临时Zillings（“临时ZILs”）不能提供、出售或转让给美国人（如1933年美国证券法第S条所定义）。 请确保在交换临时ZIL时，确认ZIL的每个持有人不是美国人（如1933年美国证券法第S条所定义）。



任何交易所的一个关键特征是能够将保管中的资金提取到用户选择的任意地址。由于Zilliqa节点不提供由交易所对交易进行签名的API，因此必须通过您选择的SDK在本地执行此操作。我们使用官方JavaScript SDK zilliqa-js进行示例。

本教程中的代码来自[示例应用程序](https://github.com/Zilliqa/dev-portal/blob/master/examples/exchange/src/services/zilliqa.ts)。

## 构造交易对象

有几种方法可以构造`Transaction`实例。 我们建议使用umbrella Zilliqa对象里的transaction factory，如下所示：

```ts
import { Zilliqa } from '@zilliqa-js/zilliqa';
const api = 'https://community-api.aws.z7a.xyz';
const zilliqa = new Zilliqa(api);
const pubKey = '02bc475d1b5dd9d6ed6347e93da3d4c1ad35f4d987d52ea91de997ecba56845cd2';

const rawTx = zilliqa.transactions.new({
  version: bytes.pack(2, 1),
  amount,
  nonce: 1
  gasLimit: Long.fromNumber(1), // normal (non-contract) transactions cost 1 gas
  gasPrice: new BN(units.toQa(1000, units.Units.Li)), // the minimum gas price is 1,000 li
  toAddr: to, // toAddr is self-explanatory
  pubKey, // this determines which account is used to send the tx
});
```

## 交易签名

另外，您还可以通过以下几种方式对交易进行签名。从原理上来说，签名用的是椭圆曲线`secp256k1`完成的。使用钱包是最简单的方法之一，让我们对上面的例子进行扩展：

```ts
/* truncated */
const privateKey = '1CC85C5F4791232D7D9A6FC35F2FF15EFAAC4A6E0E9F4A565FD2CCCCB73FCA3B'
const address = 'e3ea87d7838397fc4417f5ec449f2d2d7cdb6dd1';
zilliqa.wallet.addByPrivateKey(privateKey);
// signWith uses the specified address to perform the signing of the transaction.
// note that we provided the nonce to use when constructing the transaction.
// if the nonce is not provided, zilliqa-js will automatically try to determine the correct nonce to use.
// however, if there is no network connection, zilliqa-js will not be able to
// do that, and signing will fail.
const signedTx = await this.zil.wallet.signWith(rawTx, address);
```

请注意，在构造transaction时使用了我们提供的nonce。如果未提供nonce，zilliqa-js将自动尝试确定正确的nonce。但是，如果没有网络连接，zilliqa-js将无法做到这一点，导致签名失败。

如果`交易`已成功签名，您将能够访问`txParams`上的`signature`属性。

```ts
console.log(signedTx.txParams.signature) // 128位签名
```

在此阶段，您将能够通过种子节点将新签名的交易广播到网络。

## 发送交易

广播一个签名的交易不是重要的事情，但如果你对Zilliqa的架构没有深刻的理解，那么可能会引起一些微小的变化。

我们演示了使用内置`HTTPProvider`广播交易的低级方法，如下所示：

```ts
/* truncated */
const res = await this.zil.provider.send('CreateTransaction', tx.txParams);
```

这将返回一个`Promise`，如果成功，将包含您的交易哈希：

```ts
console.log(res.result && res.result.TranID) // 32字节交易哈希
```

但请注意，如果处理交易时出错，则返回结果中不会存在`result`。 相反，程序将返回一个`error`键，它是一个JSON-RPC 2.0的对象。

如果您收到`TranID`，则表示您的交易已被种子节点接受，现在正在等待处理。`zilliqa-js`提供了一种自动轮询查找确认的方法：

```ts
// returns a Promise<Transaction>
// in this case, we try polling the node 33 times, increasing the interval
// between attempts by 1000ms each time. this works out roughly to the block
// time on the Zilliqa main net.
const tx = await signedTx.confirm(res.result.TranID, 33, 1000)
```

`confirm`方法返回Promise，其状态表示交易的确认状态。如果交易得到确认：

```
assert(signedTx.isConfirmed() === true);
```



# 存款投票

------

#### 注意

> Zillings（“ZILs”）的临时Zillings（“临时ZILs”）不能提供、出售或转让给美国人（如1933年美国证券法第S条所定义）。 请确保在交换临时ZIL时，确认ZIL的每个持有人不是美国人（如1933年美国证券法第S条所定义）。

------

除了发送交易外，交易所还需要一种方法来监听发送到它们存款地址的交易。在本教程中，我们不会介绍在Zilliqa上类似ERC20的智能合约实现过程，但我们会采用相同的策略。

本教程中的代码来自[示例程序](https://github.com/Zilliqa/dev-portal/blob/master/examples/exchange/src/cron/deposit.ts)。

## 设置

要在Node.js中实现一个简单而熟悉的轮询机制，我们将需要额外的依赖项：

```sh
npm i node-cron p-map lodash
```

## 实现处理程序功能

我们将使用一个名为`DepositCron`的简单`class`来设置我们的cron作业。 我们将首先实现一个名为`handler`的处理程序方法。

```ts
import {flatten, range} from 'lodash';
import pMap from 'p-map';
import * as cron from 'node-cron';
import {ZilliqaService} from '../services/zilliqa';

export class DepositCron {
  addresses: string[] = [];
  frequency: string = '* * * * *';
  svc: ZilliqaService;
  task: cron.ScheduledTask;
  // you should persist the last fetched block to a database, and initialise
  // this cron job with that block number, to avoid fetch all blocks from 0 to
  // present.
  lastFetchedTxBlock: number = 0;

  constructor(frequency: string, svc: ZilliqaService, addresses: string[]) {
    this.frequency = frequency;
    this.svc = svc;
    this.addresses = addresses;
  }

  async handler() {
    const currentTxBlock = await this.svc.getTxBlock();
    console.log('Current tx block: ', currentTxBlock);
    if (currentTxBlock > this.lastFetchedTxBlock) {
      // get transactions from lastFetchedTxBlock + 1 to current, and set
      // lastFetchedTxBlock to current
      const transactions = await pMap(
        range(this.lastFetchedTxBlock + 1, currentTxBlock),
        blk => this.svc.getDeposits(this.addresses, blk),
      ).then(flatten);

      this.lastFetchedTxBlock = currentTxBlock;

      // we are only logging to stdout, but in a real application, you would
      // be writing the result to the database.
      console.log(`Found ${transactions.length} deposits for ${this.addresses}`);
    }
  }
}
```

接下来解压缩`handler`。我们采取了几个步骤：

1. 获取当前的`TxBlock`。

2. 我们将当前`TxBlock`的值与我们使用`lastFetchedTxBlock`记录的值进行比较。

3. 如果存在差异，我们将获取两者之间已处理的所有交易

   ```
   lastFetchedTxBlock + 1
   ```

   和当前

   ```
   TxBlock
   ```

   - 即每个漏掉的交易。

4. 然后，我们为在该区块范围内处理的每个交易调用`svc.getDeposits`。它将每个交易的`toAddr`属性与传递给`constructor`的`addresses`数组进行比较，检查它是否包含我们的`toAddr`。 如果包含`toAddr`，那就代表着我们所监听的地址发生了交易。

## 开始Cron工作

到目前为止，我们无法启动或控制我们的`CronJob`。 我们将通过实现`start`，`stop`和`nuke`方法来实现。

```ts
import {flatten, range} from 'lodash';
import pMap from 'p-map';
import * as cron from 'node-cron';
import {ZilliqaService} from '../services/zilliqa';

export class DepositCron {
  addresses: string[] = [];
  frequency: string = '* * * * *';
  svc: ZilliqaService;
  task: cron.ScheduledTask;
  // you should persist the last fetched block to a database, and initialise
  // this cron job with that block number, to avoid fetch all blocks from 0 to
  // present.
  lastFetchedTxBlock: number = 0;

  constructor(frequency: string, svc: ZilliqaService, addresses: string[]) {
    this.frequency = frequency;
    this.svc = svc;
    this.addresses = addresses;
    this.task = cron.schedule(this.frequency, this.handler.bind(this));
  }

  async handler() {
    const currentTxBlock = await this.svc.getTxBlock();
    console.log('Current tx block: ', currentTxBlock);
    if (currentTxBlock > this.lastFetchedTxBlock) {
      // get transactions from lastFetchedTxBlock + 1 to current, and set
      // lastFetchedTxBlock to current
      const transactions = await pMap(
        range(this.lastFetchedTxBlock + 1, currentTxBlock),
        blk => this.svc.getDeposits(this.addresses, blk),
      ).then(flatten);

      this.lastFetchedTxBlock = currentTxBlock;

      // we are only logging to stdout, but in a real application, you would
      // be writing the result to the database.
      console.log(`Found ${transactions.length} deposits for ${this.addresses}`);
    }
  }

  async start() {
    this.task.start();
  }

  async stop() {
    this.task.stop();
  }

  async nuke() {
    this.task.destroy();
  }
}
```

现在我们有了方法，我们可以像这样使用`Cronjob`：

```ts
// app.ts
// initialise services
import * as services from './services';
import * as crons from './cron';

const zilliqaSvc = new services.ZilliqaService(
  'https://stress-test-api.aws.z7a.xyz',
  {
    [config.get('mnemonic')]: 8,
  },
);

// boot up cron jobs
// these can also be destroyed
const depositCron = new crons.DepositCron('* * * * *', zilliqaSvc);
depositCron.start();
```

