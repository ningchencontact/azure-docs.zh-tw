---
title: 使用 Azure 區塊鏈服務傳送交易
description: 說明如何使用 Azure 區塊鏈服務部署智慧型合約並傳送私人交易的教學課程。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: d3ad4cdfe33948c04c278ed3dfef7aa6fda637ab
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027494"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>教學課程：使用 Azure 區塊鏈服務傳送交易

在本教學課程中，您將建立交易節點來測試合約及交易隱私權。  您將使用 Truffle 建立本機開發環境並部署智慧型合約，然後傳送私人交易。

您將學習如何：

> [!div class="checklist"]
> * 新增交易節點
> * 使用 Truffle 部署智慧型合約
> * 傳送交易
> * 驗證交易隱私權

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 完成[使用 Azure 入口網站建立區塊鏈成員](create-member.md)
* 完成[快速入門：使用 Truffle 連線至聯盟網路](connect-truffle.md)
* Truffle 需要安裝數個工具，包括 [Node.js](https://nodejs.org)、[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 和 [Truffle](https://github.com/trufflesuite/truffle)。

    若要在 Windows 10 上快速設定，請安裝 [Ubuntu on Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) 來使用 Unix Bash 殼層終端機，然後安裝 [Truffle](https://github.com/trufflesuite/truffle)。 Ubuntu on Windows 散發套件包含 Node.js 和 Git。

* 安裝 [Visual Studio Code](https://code.visualstudio.com/Download)
* 安裝 [Visual Studio Code Solidity 擴充功能](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>建立交易節點

根據預設，您會有一個交易節點。 我們會多新增兩個。 其中一個節點會參與私人交易。 另一個節點則不會包含在私人交易中。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您的 Azure 區塊鏈成員，然後選取 [交易節點] > [新增]。
1. 完成設定名為 `alpha` 的新交易節點。

    ![建立交易節點](./media/send-transaction/create-node.png)

    | 設定 | 值 | 說明 |
    |---------|-------|-------------|
    | Name | `alpha` | 交易節點名稱。 該名稱會用來建立交易節點端點的 DNS 位址。 例如： `alpha-mymanagedledger.blockchain.azure.com`。 |
    | 密碼 | 強式密碼 | 該密碼會用來存取搭配基本驗證的交易節點端點。

1. 選取 [建立] 。

    佈建一個新的交易節點大約需要 10 分鐘。

1. 重複步驟 2 到 4 以新增名為 `beta` 的交易節點。

佈建節點時，您可以繼續本教學課程。 佈建完成後，您將會有三個交易節點。

## <a name="open-truffle-project"></a>開啟 Truffle 專案

1. 開啟 Bash 殼層終端機。
1. 在下列必要條件中，將您的路徑變更為 Truffle 專案目錄：[快速入門：使用 Truffle 連線至聯盟網路](connect-truffle.md)。 例如，

    ```bash
    cd truffledemo
    ```

1. 啟動 Truffle 的互動式開發主控台。

    ``` bash
    truffle develop
    ```

    Truffle 會建立本機開發區塊鏈，並提供互動式主控台。

## <a name="connect-to-transaction-node"></a>連線到交易節點

使用 Web3 來連線至預設的交易節點並建立帳戶。 您可以從 Azure 入口網站取得 Web3 連接字串。

1. 在 Azure 入口網站中，瀏覽至預設的交易節點，然後選取 [交易節點] > [程式碼範例] > [Web3]。
1. 從 **HTTPS (存取金鑰 1)** 複製 JavaScript ![Web3 程式碼範例](./media/send-transaction/web3-code.png)

1. 將預設交易節點的 Web3 JavaScript 程式碼貼到 Truffle 互動式開發主控台。 該程式碼會建立連線至您 Azure 區塊鏈服務交易節點的 Web3 物件。

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    您可以在 Web3 物件上呼叫方法，來與交易節點互動。

1. 在預設交易節點上建立新的帳戶。 以您自己的強式密碼取代密碼參數。

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    請記下傳回的帳戶位址和您使用的密碼，以便在下一節使用。

1. 結束 Truffle 開發環境。

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>設定 Truffle 專案

若要設定 Truffle 專案，您需要 Azure 入口網站中的某些交易節點資訊。

### <a name="transaction-node-public-key"></a>交易節點公開金鑰

每個交易節點都有公開金鑰。 公開金鑰可讓您將私人交易傳送到節點。 若要將交易從預設的交易節點傳送至 alpha 交易節點，您需要 alpha 交易節點的公開金鑰。

您可以從交易節點清單中取得公開金鑰。 複製 alpha 節點的公開金鑰並儲存該值，以便稍後在本教學課程中使用。

![交易節點清單](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>交易節點端點位址

1. 在 Azure 入口網站中，瀏覽至每個交易節點，然後選取 [交易節點] > [連接字串]。
1. 針對每個交易節點，從 ** HTTPS (存取金鑰 1) 複製並儲存端點 URL。 稍後在本教學課程中，智慧型合約組態檔會需要用到端點位址。

    ![交易端點位址](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>編輯組態檔

1. 啟動 Visual Studio Code，並使用 [檔案] > [開啟資料夾] 功能表來開啟 Truffle 專案目錄資料夾。
1. 開啟 Truffle 組態檔 `truffle-config.js`。
1. 使用下列組態資訊取代檔案的內容。 新增包含端點位址和帳戶資訊的變數。 將角括號區段取代為您在上一節中收集的值。

``` javascript
var defaultnode = "<default transaction node connection string>";
var alpha = "<alpha transaction node connection string>";
var beta = "<beta transaction node connection string>";

var myAccount = "<account address>";
var myPassword = "<account password>";

var Web3 = require("web3");
```

將組態程式碼新增至組態的 **module.exports** 區段。

```javascript
module.exports = {
  networks: {
    defaultnode: {
      provider:(() =>  {
      const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);

      const web3 = new Web3(AzureBlockchainProvider);
      web3.eth.personal.unlockAccount(myAccount, myPassword);

      return AzureBlockchainProvider;
      })(),

      network_id: "*",
      gas: 0,
      gasPrice: 0,
      from: myAccount
    },
    alpha: {
      provider: new Web3.providers.HttpProvider(alpha),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    },
    beta: {
      provider: new Web3.providers.HttpProvider(beta),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    }
  }
}
```

## <a name="create-smart-contract"></a>建立智慧型合約

在**合約**資料夾中，建立名為 `SimpleStorage.sol` 的新檔案。 新增下列程式碼。

```solidity
pragma solidity >=0.4.21 <0.6.0;

contract SimpleStorage {
    string public storedData;

    constructor(string memory initVal) public {
        storedData = initVal;
    }

    function set(string memory x) public {
        storedData = x;
    }

    function get() view public returns (string memory retVal) {
        return storedData;
    }
}
```

在**移轉**資料夾中，建立名為 `2_deploy_simplestorage.js` 的新檔案。 新增下列程式碼。

```solidity
var SimpleStorage = artifacts.require("SimpleStorage.sol");

module.exports = function(deployer) {

  // Pass 42 to the contract as the first constructor parameter
  deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Account address>"})  
};
```

請取代角括弧中的值。

| 值 | 說明
|-------|-------------
| \<alpha 節點公開金鑰\> | alpha 節點的公開金鑰
| \<帳戶位址\> | 在預設交易節點中建立的帳戶位址。

在此範例中，**storeData** 值的初始值會設為 42。

**privateFor** 會定義可使用合約的節點。 在此範例中，預設交易節點的帳戶可以將私人交易投送到 **alpha** 節點。 您需要新增所有私人交易參與者的公開金鑰。 如果您未包含 **privateFor:** 和 **from:**，則智慧型合約交易會是公開狀態，能讓所有聯盟成員看見。

若要儲存所有檔案，請選取 [檔案] > [全部儲存]。

## <a name="deploy-smart-contract"></a>部署智慧型合約

使用 Truffle 將 `SimpleStorage.sol` 部署至預設的交易節點網路。

```bash
truffle migrate --network defaultnode
```

Truffle 會先編譯 **SimpleStorage** 智慧型合約，然後加以部署。

範例輸出︰

```
pat@DESKTOP:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>驗證合約隱私權

因為合約隱私權，您只能從我們在 **privateFor** 中宣告的節點中查詢合約值。 在此範例中，我們可以查詢預設交易節點，因為帳戶存在於該節點中。 使用 Truffle 主控台連線到預設的交易節點。

```bash
truffle console --network defaultnode
```

執行命令以傳回合約執行個體的值。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

如果查詢預設交易節點成功，則會傳回值 42。

範例輸出︰

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network defaultnode
truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

結束主控台。

```bash
.exit
```

由於我們已在 **privateFor** 中宣告 **alpha** 節點的公開金鑰，因此我們可以查詢 **alpha** 節點。 使用 Truffle 主控台連線到 **alpha** 節點。

```bash
truffle console --network alpha
```

執行命令以傳回合約執行個體的值。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

如果查詢 **alpha** 節點成功，則會傳回值 42。

範例輸出︰

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network alpha
truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

結束主控台。

```bash
.exit
```

我們並未在 **privateFor** 中宣告 **beta** 節點的公開金鑰，因此，因為合約隱私權，我們無法查詢 **beta** 節點。 使用 Truffle 主控台連線到 **beta** 節點。

```bash
truffle console --network beta
```

執行命令以傳回合約執行個體的值。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

查詢 **beta** 節點失敗，因為合約屬於私人。

範例輸出︰

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network beta
truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
Thrown:
Error: Returned values aren't valid, did it run Out of Gas?
    at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
    at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
    at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
    at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
```

結束主控台。

```bash
.exit
```

## <a name="send-a-transaction"></a>傳送交易

建立名為 `sampletx.js`的檔案。 請將其儲存到您的專案的根目錄。

此指令碼會將合約的 **storedData** 變數值設為 65。 將程式碼加入新檔案。

```javascript
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(done) {
  console.log("Getting deployed version of SimpleStorage...")
  SimpleStorage.deployed().then(function(instance) {
    console.log("Setting value to 65...");
    return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Account address>"});
  }).then(function(result) {
    console.log("Transaction:", result.tx);
    console.log("Finished!");
    done();
  }).catch(function(e) {
    console.log(e);
    done();
  });
};
```

取代角括弧中的值，然後儲存檔案。

| 值 | 說明
|-------|-------------
| \<alpha 節點公開金鑰\> | alpha 節點的公開金鑰
| \<帳戶位址\> | 在預設交易節點中建立的帳戶位址。

**privateFor** 會定義可使用交易的節點。 在此範例中，預設交易節點的帳戶可以將私人交易投送到 **alpha** 節點。 您需要新增所有私人交易參與者的公開金鑰。

您可以使用 Truffle 來執行預設交易節點的指令碼。

```bash
truffle exec sampletx.js --network defaultnode
```

執行命令以傳回合約執行個體的值。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

如果交易成功，則會傳回值 65。

範例輸出︰

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

結束主控台。

```bash
.exit
```

## <a name="validate-transaction-privacy"></a>驗證交易隱私權

由於交易隱私權，您只能在已於 **privateFor** 中宣告的節點上執行交易。 在此範例中，我們可以執行交易，因為我們已在 **privateFor** 中宣告 **alpha**節點的公開金鑰。 使用 Truffle 來執行 **alpha** 節點上的交易。

```bash
truffle exec sampletx.js --network alpha
```

執行命令以傳回合約執行個體的值。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

如果交易成功，則會傳回值 65。

範例輸出︰

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

結束主控台。

```bash
.exit
```

在本教學課程中，您已將兩個交易節點新增至示範合約及交易隱私權。 您已使用預設節點來部署私人的智慧型合約。 您已藉由在區塊鏈上查詢合約值和執行交易來測試隱私權。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，您可以藉由刪除 Azure 區塊鏈服務建立的 `myResourceGroup` 資源群組來刪除資源。

若要刪除資源群組：

1. 在 Azure 入口網站中，瀏覽至左側瀏覽窗格中的 [資源群組]，然後選取您想要刪除的資源群組。
1. 選取 [刪除資源群組]。 輸入資源群組名稱並選取 [刪除]，以確定進行刪除。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure 區塊鏈服務開發區塊鏈應用程式](develop.md)
