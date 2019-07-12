---
title: Azure Databricks 預先購買折扣如何套用
description: 了解 Azure Databricks 預先購買折扣如何套用至您的使用量。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827645"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Azure Databricks 預先購買折扣如何套用

您可以隨時購買期間使用預先購買的 Azure Databricks 認可單位 (DBCU)。 使用量是任何 Azure Databricks 扣除已從預先購買 DBCUs 自動。

不同於 Vm，不會每小時過期預先購買的單位。 您可以隨時購買程序期間使用它們。 若要取得預先購買折扣，您不需要重新部署，或將預先購買的方案指派給您的 Azure Databricks 工作區的使用方式。

只要預先購買折扣僅適用於 Azure Databricks 單位 (DBU) 的使用量。 其他費用，例如計算、 儲存體和網路功能是分開收費。

## <a name="pre-purchase-discount-application"></a>只要預先購買折扣應用程式

Databricks 預先購買適用於所有的 Databricks 工作負載和層級。 您可以將預購作為預付 Databricks 認可單位的集區。 使用方式是從集區，不論工作負載或階層中扣除。 使用量會以下列比例扣除：

| **工作負載** | **DBU 應用程式比例-標準層** | **DBU 應用程式比例-進階層** |
| --- | --- | --- |
| 資料分析 | 0.4 | 0.55 |
| 資料工程 | 0.15 | 0.30 |
| 輕量資料工程 | 0.07 | 0.22 |

比方說時耗用數量的資料分析-標準層，則會扣除預先購買的 Databricks 認可單位 0.4 的單位。 當數量的資料工程 Light – 會使用標準層時，預先購買的 Databricks 認可單位由 0.07 單位中扣除

## <a name="determine-plan-use"></a>決定方案使用

若要判斷您 DBCU 計劃使用，請移至 Azure 入口網站 >**保留**，按一下 購買的 Databricks 計劃。 您使用量來-會顯示日期使用任何剩餘的單位。 用於判斷保留的詳細資訊，請參閱 <<c0> [ 查看保留使用情況](billing-reservation-apis.md#see-reservation-usage)文章。

## <a name="how-discount-application-shows-in-usage-data"></a>折扣應用程式如何顯示在 使用量資料

當預先購買折扣會套用至您的 Databricks 使用量時，隨費用顯示為零的使用量資料。 如需有關保留成本和使用方式的詳細資訊，請參閱[取得 Enterprise 合約的保留項目成本和使用量](billing-understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)。
- 若要深入了解預先購買 Azure Databricks，以節省成本，請參閱[最佳化 Azure Databricks 的成本，以及讓您預購](billing-prepay-databricks-reserved-capacity.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 中的保留](billing-manage-reserved-vm-instance.md)
  - [了解預付型方案費率的訂用帳戶的保留項目使用方式](billing-understand-reserved-instance-usage.md)
  - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
