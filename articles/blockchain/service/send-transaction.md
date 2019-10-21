---
title: 使用 Visual Studio Code - Azure 區塊鏈服務
description: 本教學課程說明如何在 Visual Studio Code 中使用適用於 Ethereum 的 Azure 區塊鏈開發套件擴充功能，在 Azure 區塊鏈服務上建立、建置及部署智慧型合約。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 13a5993a14e386dc7d24c7464610bbf1ace4b9cb
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329233"
---
# <a name="tutorial-usevisual-studio-code-to-create-buildanddeploysmartcontracts"></a>教學課程：使用  Visual Studio Code 建立、建製 和 部署 智慧型 合約

在本教學課程中，在 Visual Studio Code 中使用適用於 Ethereum 的 Azure 區塊鏈開發套件擴充功能，在 Azure 區塊鏈服務上建立、建置及部署智慧型合約。 您也可使用 Truffle 以透過交易執行智慧型合約函式。

您可以使用適用於 Ethereum 的 Azure 區塊鏈服務開發套件來：

> [!div class="checklist"]
> * 建立智慧型合約
> * 部署智慧型合約
> * 透過交易執行智慧型合約函式
> * 查詢合約狀態

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 完成[快速入門：使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)

## <a name="create-a-smart-contract"></a>建立智慧型合約

適用於 Ethereum 的 Azure 區塊鏈服務開發套件會使用專案範本和 Truffle 工具來協助進行合約的 Scaffold、建置及部署。 開始之前，請完成先決條件[快速入門：使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)。 本快速入門引導您進行適用於 Ethereum 的 Azure 區塊鏈開發套件安裝和設定。

1. 從 VS Code 命令選擇區選擇 [Azure 區塊鏈服務:  新增 Solidity 專案]。
1. 選擇 [建立基本專案]  。
1. 建立名為 `HelloBlockchain` 的新資料夾，然後**選取新的專案路徑**。

Azure 區塊鏈服務開發套件會為您建立並初始化新的 Solidity 專案。 基本專案包含 **HelloBlockchain** 智慧型合約範例，以及要在 Azure 區塊鏈服務中對聯盟成員建置及部署的所有必要檔案。 可能需要數分鐘的時間才能建立好專案。 您可以選取 Azure 區塊鏈服務的輸出，以在 VS Code 的終端機面板中監視進度。

專案結構看起來會像下列範例：

   ![Solidity 專案](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>建置智慧型合約

智慧型合約位於專案的 [合約]  目錄中。 您必須先編譯智慧型合約才能將其部署至區塊鏈。 請使用 [組建合約]  命令來編譯專案中的所有智慧型合約。

1. 在 VS Code 總管的提要欄位中，展開專案中的 [合約]  資料夾。
1. 對 [HelloBlockchain.sol]  按一下滑鼠右鍵，然後從功能表中選擇 [建置合約]  。

    ![建置合約](./media/send-transaction/build-contracts.png)

Azure 區塊鏈服務開發套件會使用 Truffle 來編譯智慧型合約。

![編譯輸出](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>部署智慧型合約

Truffle 會使用移轉指令碼將合約部署至 Ethereum 網路。 移轉項目是位於專案 [移轉]  目錄中的 JavaScript 檔案。

1. 若要部署智慧型合約，請對 [HelloBlockchain.sol]  按一下滑鼠右鍵，然後從功能表中選擇 [部署合約]  。
1. 請在命令選擇區中選擇您的 Azure 區塊鏈聯盟網路。 當您建立專案時，系統會在專案的 Truffle 設定檔中新增聯盟區塊鏈網路。
1. 選擇 [產生助憶檔]  。 選擇檔案名稱，並將助憶檔案儲存在專案資料夾中。 例如： `myblockchainmember.env` 。 助憶檔案可用來為區塊鏈成員產生 Ethereum 私密金鑰。

Azure 區塊鏈服務開發套件會使用 Truffle 來執行移轉指令碼，以將合約部署至區塊鏈。

![已成功部署的合約](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>呼叫合約函式

**HelloBlockchain** 合約的 **SendRequest** 函式會變更 **RequestMessage** 狀態變數。 區塊鏈網路狀態的變更會透過交易來完成。 您可以建立指令碼來透過交易執行 **SendRequest** 函式。

1. 在 Truffle 專案的根目錄中建立新的檔案，並將其命名為 `sendrequest.js`。 在該檔案中新增下列 Web3 JavaScript 程式碼。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. 當 Azure 區塊鏈服務開發套件建立專案時，系統會使用聯盟區塊鏈網路端點的詳細資料來產生 Truffle 設定檔。 在專案中開啟 **truffle-config.js**。 設定檔會列出兩個網路：一個命名為「開發」，一個則具有和聯盟相同的名稱。
1. 在 VS Code 的終端機窗格中，使用 Truffle 在聯盟區塊鏈網路上執行指令碼。 在終端機窗格的功能表列中，於下拉式清單內選取 [終端機]  索引標籤和 [PowerShell]  。

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    將 \<blockchain network\> 取代為 **truffle-config.js** 中所定義區塊鏈網路的名稱。

Truffle 就會在區塊鏈網路上執行指令碼。

![指令碼輸出](./media/send-transaction/execute-transaction.png)

當您透過交易執行合約的函式時，系統要等到建立區塊後才會處理交易。 應該會透過交易來執行的函式會傳回交易識別碼，而非傳回值。

## <a name="query-contract-state"></a>查詢合約狀態

智慧型合約函式可以傳回狀態變數的目前值。 讓我們新增函式以傳回狀態變數的值。

1. 在 [HelloBlockchain.sol]  中，將 **getMessage** 函式新增至 **HelloBlockchain** 智慧型合約。

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    函式會根據合約的目前狀態，傳回狀態變數中所儲存的訊息。

1. 以滑鼠右鍵按一下 [HelloBlockchain.sol]  ，然後從功能表中選擇 [建置合約]  來編譯智慧型合約的變更。
1. 若要部署，請對 [HelloBlockchain.sol]  按一下滑鼠右鍵，然後從功能表中選擇 [部署合約]  。 出現提示時，請在命令選擇區中選擇您的 Azure 區塊鏈聯盟網路。
1. 接下來，使用 **getMessage** 函式的呼叫來建立指令碼。 在 Truffle 專案的根目錄中建立新的檔案，並將其命名為 `getmessage.js`。 在該檔案中新增下列 Web3 JavaScript 程式碼。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. 在 VS Code 的終端機窗格中，使用 Truffle 在區塊鏈網路上執行指令碼。 在終端機窗格的功能表列中，於下拉式清單內選取 [終端機]  索引標籤和 [PowerShell]  。

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    將 \<blockchain network\> 取代為 **truffle-config.js** 中所定義區塊鏈網路的名稱。

指令碼會藉由呼叫 getMessage 函式來查詢智慧型合約。 系統會傳回 **RequestMessage** 狀態變數的目前值。

![指令碼輸出](./media/send-transaction/execute-get.png)

請注意，此值不是 **Hello, blockchain!** 。 相反地，所傳回的值是預留位置。 當您變更和部署合約時，合約會取得新的合約位址，而狀態變數則會是智慧型合約函式中的指派值。 Truffle 範例 **2_deploy_contracts.js** 移轉指令碼會部署智慧型合約，並以引數的形式傳遞預留位置值。 建構函式會將 **RequestMessage** 狀態變數設定為預留位置值，系統所傳回的便是此值。

1. 若要設定 **RequestMessage** 狀態變數並查詢值，請再次執行 **sendrequest.js** 和 **getmessage.js** 指令碼。

    ![指令碼輸出](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** 會將 **RequestMessage** 狀態變數設定為 **Hello, blockchain!** ，而且 **getmessage.js** 會查詢合約中是否有 **RequestMessage** 狀態變數的值，並傳回 **Hello, blockchain!** 。

## <a name="clean-up-resources"></a>清除資源

您可以刪除不再需要的資源，方法是刪除您在*建立區塊鏈成員*必要條件快速入門中所建立的 `myResourceGroup` 資源群組。

若要刪除資源群組：

1. 在 Azure 入口網站中，瀏覽至左側瀏覽窗格中的 [資源群組]  ，然後選取您想要刪除的資源群組。
1. 選取 [刪除資源群組]  。 輸入資源群組名稱並選取 [刪除]  ，以確定進行刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure 區塊鏈服務開發套件建立 Solidity 專案範例。 您已建置並部署智慧型合約，然後在裝載於 Azure 區塊鏈服務的區塊鏈聯盟網路上，透過交易呼叫了函式。

> [!div class="nextstepaction"]
> [使用 Azure 區塊鏈服務開發區塊鏈應用程式](develop.md)
