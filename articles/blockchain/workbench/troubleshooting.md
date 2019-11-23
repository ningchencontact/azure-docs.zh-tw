---
title: Azure Blockchain Workbench 疑難排解
description: How to troubleshoot an Azure Blockchain Workbench Preview application.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324309"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Preview troubleshooting

我們提供了 PowerShell 指令碼來協助開發人員偵錯或支援。 指令碼會產生摘要，並收集詳細記錄以供疑難排解。 所收集的記錄包括：

* 區塊鏈網路，例如 Ethereum
* Blockchain Workbench 微服務
* Application Insights
* Azure Monitoring (Azure Monitor logs)

您可以使用這些資訊來判斷後續步驟，並判斷問題的根本原因。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>疑難排解指令碼

GitHub 上有提供 PowerShell 疑難排解指令碼。 [下載 zip 檔案](https://github.com/Azure-Samples/blockchain/archive/master.zip)，或從 GitHub 複製範例。

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>執行指令碼
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

執行 `collectBlockchainWorkbenchTroubleshooting.ps1` 指令碼來收集記錄，並建立包含疑難排解資訊資料夾的 ZIP 檔案。 例如：

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
此指令碼接受下列參數︰

| 參數  | 描述 | 必要項 |
|---------|---------|----|
| SubscriptionID | 用來建立或找到所有資源的 SubscriptionID。 | 是 |
| resourceGroupName | Blockchain Workbench 部署所在 Azure 資源群組的名稱。 | 是 |
| OutputDirectory | 用來建立輸出 .ZIP 檔案的路徑。 如果未指定，則會預設為目前的目錄。 | 否 |
| LookbackHours | 提取遙測資料時要使用的小時數。 預設值為 24 小時。 最大值為 90 小時 | 否 |
| OmsSubscriptionId | The subscription ID where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| 否 |
| OmsResourceGroup |The resource group where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| 否 |
| OmsWorkspaceName | Log Analytics 工作區名稱。 Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group | 否 |

## <a name="what-is-collected"></a>所收集的項目有哪些？

輸出 ZIP 檔案包含下列資料夾結構：

| 資料夾或檔案 | 描述  |
|---------|---------|
| \Summary.txt | 系統的摘要 |
| \Metrics\blockchain | 區塊鏈的相關計量 |
| \Metrics\Workbench | Workbench 的相關計量 |
| \Details\Blockchain | 區塊鏈的詳細記錄 |
| \Details\Workbench | Workbench 的詳細記錄 |

摘要檔可讓您快速了解應用程式的整體狀態和應用程式的健康情況。 摘要會提供建議的動作，指出最常發生的錯誤以及有關執行中服務的中繼資料。

**計量**資料夾包含一段時間中各種不同系統元件的計量。 例如，輸出檔 `\Details\Workbench\apiMetrics.txt` 包含不同回應碼的摘要，以及收集期間的回應時間。 **詳細資料**資料夾包含詳細的記錄，可用來針對特定的 Workbench 或基礎區塊鏈網路問題進行移難排解。 例如，`\Details\Workbench\Exceptions.csv` 包含最近出現在系統中的例外狀況清單，在透過智慧合約或與區塊鏈互動來對錯誤進行疑難排解時，此項目十分實用。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench Application Insights 疑難排解指南](https://aka.ms/workbenchtroubleshooting) \(英文\)
