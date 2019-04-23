---
title: Azure 資訊保護資料連接至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將 Azure 資訊保護中 Azure Sentinel 的資料連接。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 2f970910e19b3c1ed9d262d356c49848f4248b09
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790836"
---
# <a name="connect-data-from-azure-information-protection"></a>將資料連接，從 Azure 資訊保護

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以從串流記錄[Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)到 Azure Sentinel 只要按一下。 Azure 資訊保護可協助保護您的資料，無論是儲存在雲端或內部部署基礎結構和控制項，並協助保護電子郵件、 文件和您的公司外部共用的敏感性資料。 從簡易的分類到內嵌的標籤和權限，加強資料保護隨時都與 Azure 資訊保護。 當您連接 Azure 資訊保護到 Azure Sentinel，您的資料流的所有警示從 Azure 資訊保護至 Azure 的 Sentinel。


## <a name="prerequisites"></a>必要條件

- 使用全域管理員、 安全性系統管理員或資訊的保護權限的使用者


## <a name="connect-to-azure-information-protection"></a>連線至 Azure Information Protection

如果您已經有 Azure Information Protection，請確定它是[在您網路上啟用](https://docs.microsoft.com/azure/information-protection/activate-service)。
如果在部署 Azure 資訊保護，而且取得資料，請參閱警示的資料可以輕鬆地串流到 Azure 的 Sentinel。


1. 在 Azure Sentinel，選取**資料連接器**，然後按一下**Azure Information Protection**圖格。

2. 移至[Azure Information Protection 入口網站](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. 底下**連接**，設定 Azure Information protection 的記錄檔，以 Azure Sentinel 的資料流，依序按一下[設定分析](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. 選取您已部署至 Azure 的 Sentinel 的工作區。 

5. 按一下 [確定]。

6. 若要使用 Log Analytics 中的 Azure Information Protection 的警示相關的結構描述，搜尋**InformationProtectionLogs_CL**。




## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接到 Azure Sentinel 的 Azure 資訊保護。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
