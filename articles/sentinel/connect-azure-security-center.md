---
title: 收集 Azure Sentinel 預覽版中的 Azure 資訊安全中心 |Microsoft Docs
description: 了解如何收集在 Azure Sentinel 的 Azure 資訊安全中心資料。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f59c5f6a9f497a6420172996f9f327f16ffd26f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242267"
---
# <a name="collect-data-from-azure-security-center"></a>從 Azure 資訊安全中心收集資料

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Azure 的 Sentinel 可讓您收集的警示[Azure 資訊安全中心](../security-center/security-center-intro.md)並將它們串流至 Azure 的 Sentinel。 

## <a name="prerequisites"></a>必要條件

- 如果您想要匯出從 Azure 資訊安全中心的警示，您必須是訂用帳戶您串流處理的記錄檔上的參與者。

- 您必須擁有[Azure 資訊安全中心標準層](../security-center/security-center-pricing.md)訂用帳戶上執行。 否則，請[訂用帳戶升級為標準](https://azure.microsoft.com/pricing/details/security-center/)。

- 您必須登入您想要連線的每個訂用帳戶具有全域管理員或安全性系統管理員權限的使用者。


## <a name="connect-to-azure-security-center"></a>連接到 Azure 資訊安全中心

1. 在 Azure Sentinel，選取**資料收集**，然後按一下**Azure 資訊安全中心**圖格。
1. 在右側，按一下**Connect**每個訂用帳戶的警示，您想要串流處理至 Azure 的 Sentinel 旁邊。 請確定每個訂用帳戶升級到 Azure 資訊安全中心標準層，以資料流的警示，以便 Azure Sentinel。

3. 按一下 [ **連接**]。

4. 若要使用 Log Analytics 中的 Azure 資訊安全中心警示相關的結構描述，搜尋**SecurityEvent**。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接到 Azure Sentinel 的 Azure 資訊安全中心。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
