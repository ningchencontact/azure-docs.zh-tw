---
title: Configure Blockchain Data Manager using Azure portal - Azure Blockchain Service
description: Create and manage Blockchain Data Manager for Azure Blockchain Service using the Azure portal.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 9c682f449fbab823134d626870c7dcfe8a8f2847
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455822"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>使用 Azure 入口網站設定區塊鏈資料管理員

Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data and send it to an Azure Event Grid Topic.

To configure a Blockchain Data Manager instance, you:

* Create a Blockchain Data Manager instance for an Azure Blockchain Service transaction node
* Add your blockchain applications

## <a name="prerequisites"></a>必要條件

* Complete [Quickstart: Create a blockchain member using the Azure portal](create-member.md) or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI](create-member-cli.md)
* 建立[事件方格主題](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件方格中的事件處理常式](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>建立執行個體

區塊鏈資料管理員執行個體都連線至 Azure 區塊鏈服務交易節點，並加以監視。 Only users with access to the transaction node can create a connection. 執行個體會從該交易節點中擷取所有的原始區塊和原始交易資料。

輸出連線會將區塊鏈資料傳送至 Azure 事件方格。 您在建立執行個體時，會設定單一輸出連線。 區塊鏈資料管理員會針對任何指定的區塊鏈資料管理員執行個體，支援多個事件方格主題輸出連線。 您可將區塊鏈資料傳送至單一目的地，或將區塊鏈資料傳送至多個目的地。 To add another destination, just add additional outbound connections to the instance.

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. Go to the Azure Blockchain Service member you want to connect to Blockchain Data Manager. 選取 [區塊鏈資料管理員]。
1. 選取 [新增]。

    ![新增區塊鏈資料管理員](./media/data-manager-portal/add-instance.png)

    輸入下列詳細資料：

    設定 | 描述
    --------|------------
    Name | 為已連線的區塊鏈資料管理員輸入唯一名稱。 The Blockchain Data Manager name can contain lower case letters and numbers and has a maximum length of 20 characters.
    交易節點 | Choose a transaction node. Only transaction nodes you have read access are listed.
    連接名稱 | 為用來傳送區塊鏈交易資料的輸出連線輸入唯一名稱。
    事件方格端點 | Choose an event grid topic in the same subscription as the Blockchain Data Manager instance.

1. 選取 [確定]。

    區塊鏈資料管理員執行個體不到一分鐘即可建立。 執行個體在部署後將會自動啟動。 A running Blockchain Data Manager instance captures blockchain events from the transaction node and sends data to the outbound connections.

    The new instance appears in the list of Blockchain Data Manager instances for the Azure Blockchain Service member.

    ![List of Blockchain Data Member instances](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Add blockchain application

If you add a blockchain application, Blockchain Data Manager decodes event and property state for the application. Otherwise, only raw block and raw transaction data is sent. Blockchain Data Manager also discovers contract addresses when the contract is deployed. You can add multiple blockchain applications to a Blockchain Data Manager instance.

> [!IMPORTANT]
> Currently, blockchain applications that declare Solidity [array types](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) or [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) are not fully supported. Properties declared as array or mapping types will not be decoded in *ContractPropertiesMsg* or *DecodedContractEventsMsg* messages.

Blockchain Data Manager requires a smart contract ABI and deployed bytecode file to add the application.

### <a name="get-contract-abi-and-bytecode"></a>Get Contract ABI and bytecode

合約 ABI 會定義智慧型合約的介面。 此介面會說明如何與智慧型合約互動。 您可以使用[適用於 Ethereum 的 Azure 區塊鏈服務開發套件擴充功能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)，將合約 ABI 複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製合約 ABI]。

    ![Visual Studio Code 窗格，已選取 [複製合約 ABI]](./media/data-manager-portal/abi-devkit.png)

    合約 ABI 會複製到剪貼簿中。

1. 將 **abi** 陣列儲存為 JSON 檔案。 例如 *abi.json*。 您在後續步驟中會用到此檔案。

區塊鏈資料管理員需要將已部署的位元組程式碼用於智慧型合約。 已部署的位元組程式碼與智慧型合約位元組程式碼不同。 您可以從已編譯的合約中繼資料檔案中取得已部署的位元組程式碼。

1. 開啟您 Solidity 專案的 **build/contracts** 資料夾中包含的合約中繼資料檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 在 JSON 檔案中找出 **deployedBytecode** 元素。
1. 複製不含引號的十六進位值。

    ![Visual Studio Code 窗格，具有中繼資料內的位元組程式碼](./media/data-manager-portal/bytecode-metadata.png)

1. 將**位元組程式碼**值儲存為 JSON 檔案。 例如 *bytecode.json*。 您在後續步驟中會用到此檔案。

下列範例顯示在 VS Code 編輯器中開啟的 *abi.json* 和 *bytecode.json* 檔案。 您的檔案應會與其相似。

![abi.json 和 bytecode.json 檔案的範例](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>建立合約 ABI 和位元組程式碼 URL

區塊鏈資料管理員在新增應用程式時，必須可透過 URL 存取合約 ABI 和位元組程式碼檔案。 您可以使用 Azure 儲存體帳戶來提供可私下存取的 URL。

#### <a name="create-storage-account"></a>建立儲存體帳戶

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>上傳合約檔案

1. 為儲存體帳戶建立新的容器。 選取 [容器] > [容器]。

    ![建立儲存體帳戶容器](./media/data-manager-portal/create-container.png)

    | 欄位 | 描述 |
    |-------|-------------|
    | Name  | 為容器命名。 例如 *smartcontract* |
    | 公用存取層級 | 選擇 [私人 (沒有匿名存取)] |

1. 選取 [確定] 以建立容器。
1. 選取容器，然後選取 [上傳]。
1. 選擇您在[取得合約 ABI 和位元組程式碼](#get-contract-abi-and-bytecode)一節中所建立的兩個 JSON 檔案。

    ![上傳 Blob](./media/data-manager-portal/upload-blobs.png)

    選取 [上傳]。

#### <a name="generate-url"></a>產生 URL

為每個 Blob 分別產生一個共用存取簽章。

1. 選取 ABI JSON Blob。
1. 選取 [產生 SAS]
1. 設定所需的存取簽章到期日，然後選取 [產生 Blob SAS 權杖和 URL]。

    ![產生 SAS Token](./media/data-manager-portal/generate-sas.png)

1. 複製 **Blob SAS URL** 並加以儲存，以供下一節使用。
1. 針對位元組程式碼 JSON Blob，重複[產生 URL](#generate-url) 步驟。

### <a name="add-application-to-instance"></a>Add application to instance

1. 從執行個體清單中，選取您的區塊鏈資料管理員執行個體。
1. 選取 [區塊鏈應用程式]。
1. 選取 [新增]。

    ![新增區塊鏈應用程式](./media/data-manager-portal/add-application.png)

    輸入區塊鏈應用程式的名稱，以及智慧型合約 ABI 和位元組程式碼 URL。

    設定 | 描述
    --------|------------
    Name | 輸入要追蹤的區塊鏈應用程式的唯一名稱。
    合約 ABI | 合約 ABI 檔案的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組程式碼 URL](#create-contract-abi-and-bytecode-url)。
    合約位元組程式碼 | 位元組程式碼檔案的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組程式碼 URL](#create-contract-abi-and-bytecode-url)。

1. 選取 [確定]。

    應用程式建立後，應用程式會出現在區塊鏈應用程式清單中。

    ![區塊鏈應用程式清單](./media/data-manager-portal/artifact-list.png)

您可以刪除 Azure 儲存體帳戶，或用它來設定更多區塊鏈應用程式。 如果您要刪除 Azure 儲存體帳戶，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

## <a name="stop-instance"></a>Stop instance

Stop the Blockchain Manager instance when you want to stop capturing blockchain events and sending data to the outbound connections. When the instance is stopped, no charges are incurred for Blockchain Data Manager. 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/blockchain-service)。

1. Go to **Overview** and select **Stop**.

    ![Stop instance](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>後續步驟

Try the next tutorial creating a blockchain transaction message explorer using Blockchain Data Manager and Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)