---
title: Azure Blockchain Workbench 疑難排解
description: 如何針對 Azure Blockchain Workbench 應用程式進行疑難排解。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Azure Blockchain Workbench 疑難排解

我們提供了 PowerShell 指令碼來協助開發人員偵錯或支援。 指令碼會產生摘要，並收集詳細記錄以供疑難排解。 所收集的記錄包括：

* 區塊鏈網路，例如 Ethereum
* Blockchain Workbench 微服務
* Application Insights
* Azure 監視 (OMS)

您可以使用這些資訊來判斷後續步驟，並判斷問題的根本原因。 

## <a name="troubleshooting-script"></a>疑難排解指令碼

GitHub 上有提供 PowerShell 疑難排解指令碼。 [下載 zip 檔案](https://github.com/Azure-Samples/blockchain/archive/master.zip)，或從 GitHub 複製範例。

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>執行指令碼
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

執行 `collectBlockchainWorkbenchTroubleshooting.ps1` 指令碼來收集記錄，並建立包含疑難排解資訊資料夾的 ZIP 檔案。 例如︰

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
此指令碼接受下列參數︰

| 參數  | 說明 | 必要 |
|---------|---------|----|
| SubscriptionID | 用來建立或找到所有資源的 SubscriptionID。 | yes |
| resourceGroupName | Blockchain Workbench 部署所在 Azure 資源群組的名稱。 | yes |
| OutputDirectory | 用來建立輸出 .ZIP 檔案的路徑。 如果未指定，則會預設為目前的目錄。 | 否
| OmsSubscriptionId | OMS 部署所在的訂用帳戶識別碼。 只有在區塊鏈網路的 OMS 是部署在 Blockchain Workbench 資源群組之外時，才傳遞此參數。| 否 |
| OmsResourceGroup |OMS 部署所在的資源群組。 只有在區塊鏈網路的 OMS 是部署在 Blockchain Workbench 資源群組之外時，才傳遞此參數。| 否 |
| OmsWorkspaceName | OMS 工作區名稱。 只有在區塊鏈網路的 OMS 是部署在 Blockchain Workbench 資源群組之外時，才傳遞此參數 | 否 |

## <a name="what-is-collected"></a>所收集的項目有哪些？

輸出 ZIP 檔案包含下列資料夾結構：

| 資料夾\檔案 | 說明  |
|---------|---------|
| \Summary.txt | 系統的摘要 |
| \metrics\blockchain | 區塊鏈的相關計量 |
| \metrics\workbench | Workbench 的相關計量 |
| \details\blockchain | 區塊鏈的詳細記錄 |
| \details\workbench | Workbench 的詳細記錄 |

摘要檔可讓您快速了解應用程式的整體狀態和應用程式的健康情況。 摘要會提供建議的動作，指出最常發生的錯誤以及有關執行中服務的中繼資料。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 架構](blockchain-workbench-architecture.md)