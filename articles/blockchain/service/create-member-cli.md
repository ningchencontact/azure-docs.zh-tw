---
title: 使用 Azure CLI 建立 Azure 區塊鏈服務
description: 使用 Azure CLI 在 Azure 區塊鏈服務中建立區塊鏈成員。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 88b0336d7be1852d1bb2ae522f3d37ebed8d1fa0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029986"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure 區塊鏈服務的區塊鏈成員

Azure 區塊鏈服務是一個區塊鏈平台，可用來執行您在智慧型合約內的商務邏輯。 此快速入門說明如何使用 Azure CLI 建立區塊鏈成員以開始使用產品。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.51 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>建立區塊鏈成員

在 Azure 區塊鏈服務中，建立在新的聯盟中執行仲裁總帳通訊協定的區塊鏈成員。

您必須傳入數個參數和屬性。 請將下列參數取代為您自己的值。

| 參數 | 說明 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 請使用您在上一節中建立的資源群組。
| **name** | 可識別 Azure 區塊鏈服務區塊鏈成員的唯一名稱。 用於公開端點位址的名稱。 例如： `myblockchainmember.blockchain.azure.com`。
| **位置** | 區塊鏈成員建立所在的 Azure 區域。 例如： `eastus`。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。
| **password** | 成員帳戶密碼。 成員帳戶密碼會用來對使用基本驗證的區塊鏈成員公用端點進行驗證。
| **consortium** | 要加入或建立的聯盟名稱。
| **consortiumManagementAccountPassword** | 聯盟管理密碼。 此密碼用來聯結聯盟。
| **skuName** | 服務層級類型。 使用 S0 表示「標準」，B0 表示「基本」。

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

建立區塊鏈成員和支援的資源需要約 10 分鐘的時間。

以下顯示成功建立作業的範例輸出。

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>清除資源

您可以使用您建立的區塊鏈成員來進行下一個快速入門或教學課程。 不再需要資源時，您可以藉由刪除 Azure 區塊鏈服務建立的 `myResourceGroup` 資源群組來刪除資源。

執行下列命令，以移除資源群組和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

現在您已建立區塊鏈成員，接下來請嘗試進行 [Geth](connect-geth.md)、[MetaMask](connect-metamask.md) 或 [Truffle](connect-truffle.md) 的「連線到交易節點快速入門」。

> [!div class="nextstepaction"]
> [使用 Truffle 連線至 Azure 區塊鏈服務網路](connect-truffle.md)
