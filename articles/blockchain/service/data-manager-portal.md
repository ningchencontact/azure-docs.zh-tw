---
title: 設定區塊鏈資料管理員-Azure 入口網站
description: 如何使用 Azure 入口網站建立和管理區塊鏈資料管理員。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 484322fb0486eeb4ab67366d32350c69a18da743
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605930"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>使用 Azure 入口網站設定區塊鏈資料管理員

設定 Azure 區塊鏈 Service 的區塊鏈資料管理員來捕獲區塊鏈資料，並將其傳送至 Azure 事件方格主題。

若要設定區塊鏈資料管理員實例，您可以：

* 建立 Azure 區塊鏈 Service 交易節點的區塊鏈資料管理員實例
* 新增您的區塊鏈應用程式

## <a name="prerequisites"></a>必要條件

* 完成[快速入門：使用 Azure 入口網站或快速入門建立區塊鏈成員](create-member.md) [：使用 Azure CLI 建立 Azure 區塊鏈 Service 區塊鏈成員](create-member-cli.md)
* 建立[事件方格主題](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 瞭解[Azure 事件方格中的事件處理常式](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>建立實例

區塊鏈資料管理員實例會連接並監視 Azure 區塊鏈 Service 交易節點。 只有具有交易節點存取權的使用者才能建立連線。 實例會從交易節點中捕捉所有原始區塊和未經處理的交易資料。

輸出連線會將區塊鏈資料傳送至 Azure 事件方格。 當您建立實例時，您會設定單一輸出連接。 區塊鏈資料管理員針對任何指定的區塊鏈資料管理員實例支援多個事件方格主題輸出連接。 您可以將區塊鏈資料傳送至單一目的地，或將區塊鏈資料傳送至多個目的地。 若要加入另一個目的地，只要將額外的輸出連線加入實例。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您想要連接到區塊鏈資料管理員的 Azure 區塊鏈服務成員。 選取 [**區塊鏈資料管理員**]。
1. 選取 [新增]。

    ![新增區塊鏈資料管理員](./media/data-manager-portal/add-instance.png)

    輸入下列詳細資料：

    設定 | 說明
    --------|------------
    名稱 | 為已連線的區塊鏈資料管理員輸入唯一的名稱。 區塊鏈資料管理員名稱可包含小寫字母和數位，最大長度為20個字元。
    交易節點 | 選擇交易節點。 只會列出您擁有讀取權限的交易節點。
    連接名稱 | 輸入傳送區塊鏈交易資料的輸出連線唯一名稱。
    事件方格端點 | 在與區塊鏈資料管理員實例相同的訂用帳戶中，選擇事件方格主題。

1. 選取 [確定]。

    建立區塊鏈資料管理員實例需要不到一分鐘的時間。 部署實例之後，它就會自動啟動。 執行中的區塊鏈資料管理員實例會從交易節點中捕捉區塊鏈事件，並將資料傳送至輸出連線。

    新的實例會出現在 Azure 區塊鏈服務成員的區塊鏈資料管理員實例清單中。

    ![區塊鏈資料成員實例的清單](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>新增區塊鏈應用程式

如果您新增區塊鏈應用程式，區塊鏈資料管理員會將應用程式的事件和屬性狀態解碼。 否則，只會傳送原始區塊和原始交易資料。 區塊鏈資料管理員也會在部署合約時探索合約位址。 您可以將多個區塊鏈應用程式新增至區塊鏈資料管理員實例。

> [!IMPORTANT]
> 目前，不完全支援宣告密度[陣列類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays)或[對應類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)的區塊鏈應用程式。 宣告為數組或對應類型的屬性將不會在*ContractPropertiesMsg*或*DecodedContractEventsMsg*訊息中解碼。

區塊鏈資料管理員需要智慧型合約 ABI 並部署的位元組程式碼檔案，才能新增應用程式。

### <a name="get-contract-abi-and-bytecode"></a>取得合約 ABI 和位元組碼

合約 ABI 會定義智慧合約介面。 其中描述如何與智慧合約互動。 您可以使用[乙太坊延伸](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)模組的 Azure 區塊鏈開發套件，將合約 ABI 複製到剪貼簿。

1. 在 [Visual Studio Code explorer] 窗格中，展開您的密度專案的 [**組建/合約**] 資料夾。
1. 以滑鼠右鍵按一下 [合約中繼資料 JSON 檔案]。 檔案名是智慧合約名稱，後面接著**json**延伸模組。
1. 選取 [**複製合約 ABI**]。

    ![含有複製合約 ABI 選擇的 Visual Studio Code 窗格](./media/data-manager-portal/abi-devkit.png)

    合約 ABI 會複製到剪貼簿。

1. 將**abi**陣列儲存為 JSON 檔案。 例如 abi. *json*。 您在稍後的步驟中會用到此檔案。

區塊鏈資料管理員需要智慧合約的已部署位元組程式碼。 部署的位元組和智慧合約的位元組碼不同。 您可以從已編譯的合約中繼資料檔案取得已部署的位元組。

1. 開啟包含在您的密度專案的 [**組建/合約**] 資料夾中的合約中繼資料檔案。 檔案名是智慧合約名稱，後面接著**json**延伸模組。
1. 尋找 JSON 檔案中的**deployedBytecode**元素。
1. 複製不含引號的十六進位值。

    ![在中繼資料中具有位元組的 Visual Studio Code 窗格](./media/data-manager-portal/bytecode-metadata.png)

1. 將 [**位元組碼**] 值儲存為 JSON 檔案。 例如，位元組程式碼 *。* 您在稍後的步驟中會用到此檔案。

下列範例會顯示在 [VS Code 編輯器] 中開啟的*abi*和*位元組檔. json*檔案。 您的檔案看起來應該類似。

![Abi 和位元組檔 json 檔案的範例](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>建立合約 ABI 和位元組的 URL

區塊鏈資料管理員需要在新增應用程式時，由 URL 存取合約 ABI 和位元組程式碼檔案。 您可以使用 Azure 儲存體帳戶來提供可私下存取的 URL。

#### <a name="create-storage-account"></a>建立儲存體帳戶

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>上傳合約檔案

1. 為儲存體帳戶建立新的容器。 選取容器 **> 容器**。

    ![建立儲存體帳戶容器](./media/data-manager-portal/create-container.png)

    | 欄位 | 說明 |
    |-------|-------------|
    | 名稱  | 將容器命名為。 例如， *smartcontract* |
    | 公用存取層級 | 選擇 [*私人] （沒有匿名存取）* |

1. 選取 [確定] 以建立容器。
1. 選取容器，然後選取 [**上傳**]。
1. 選擇您在[取得合約 ABI 和位元組](#get-contract-abi-and-bytecode)檔一節中建立的 JSON 檔案。

    ![上傳 Blob](./media/data-manager-portal/upload-blobs.png)

    選取 [上傳]。

#### <a name="generate-url"></a>產生 URL

針對每個 blob，產生共用存取簽章。

1. 選取 ABI JSON blob。
1. 選取 [**產生 SAS** ]
1. 設定所需的存取簽章到期日，然後選取 [**產生 BLOB SAS 權杖和 URL**]。

    ![產生 SAS 權杖](./media/data-manager-portal/generate-sas.png)

1. 複製**BLOB SAS URL**並加以儲存，以供下一節。
1. 針對 [位元組程式碼] JSON blob 重複 [[產生 URL](#generate-url) ] 步驟。

### <a name="add-application-to-instance"></a>將應用程式新增至實例

1. 從 [實例] 清單中選取您的區塊鏈資料管理員實例。
1. 選取 [**區塊鏈應用程式**]。
1. 選取 [新增]。

    ![新增區塊鏈應用程式](./media/data-manager-portal/add-application.png)

    輸入區塊鏈應用程式的名稱和智慧型合約 ABI 和位元組程式碼 Url。

    設定 | 說明
    --------|------------
    名稱 | 輸入要追蹤之區塊鏈應用程式的唯一名稱。
    合約 ABI | 合約 ABI 檔案的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組](#create-contract-abi-and-bytecode-url)程式碼 URL。
    合約位元組碼 | 位元組檔的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組](#create-contract-abi-and-bytecode-url)程式碼 URL。

1. 選取 [確定]。

    建立應用程式之後，應用程式會出現在區塊鏈應用程式清單中。

    ![區塊鏈應用程式清單](./media/data-manager-portal/artifact-list.png)

您可以刪除 Azure 儲存體帳戶，或使用它來設定更區塊鏈的應用程式。 如果您想要刪除 Azure 儲存體帳戶，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

## <a name="stop-instance"></a>停止實例

當您想要停止捕獲區塊鏈事件並將資料傳送到輸出連線時，請停止區塊鏈 Manager 實例。 當實例停止時，區塊鏈資料管理員不會產生任何費用。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/blockchain-service)。

1. 移至 **[總覽**]，然後選取 [**停止**]。

    ![停止實例](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>後續步驟

嘗試使用區塊鏈資料管理員和 Azure Cosmos DB 建立區塊鏈交易訊息瀏覽器。

> [!div class="nextstepaction"]
> [教學課程：使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB](data-manager-cosmosdb.md)