---
title: 將具有預先購買的 Azure Databricks 成本最佳化
description: 了解如何您可以預先支付 Azure Databricks 費用與保留的容量，以節省成本。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811254"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>將具有預先購買的 Azure Databricks 成本最佳化

您可以將儲存在 Azure Databricks 單位 (DBU) 成本預先購買 Azure Databricks 時認可單位 (DBCU) 的一或三年。 您可以使用預先購買的 DBCUs 隨時購買期間。 不同於 Vm，每小時不會過期的預先購買的單位，並隨時購買程序期間使用它們。

Azure Databricks 中使用任何扣除已從預先購買 Dbu 自動。 您不需要重新部署，或將預先購買的方案指派給您的 Azure Databricks 工作區，以取得預先購買折扣 DBU 的使用方式。

只要預先購買折扣僅適用於 DBU 使用量。 其他費用，例如計算、 儲存體和網路功能是分開收費。

## <a name="determine-the-right-size-to-buy"></a>判斷購買的適當大小

Databricks 預先購買適用於所有的 Databricks 工作負載和層級。 您可以將預購作為預付 Databricks 認可單位的集區。 使用方式是從集區，不論工作負載或階層中扣除。 使用量會以下列比例扣除：

| **工作負載** | **DBU 應用程式比標準層** | **DBU 應用程式比例-進階層** |
| --- | --- | --- |
| 資料分析 | 0.4 | 0.55 |
| 資料工程 | 0.15 | 0.30 |
| 輕量資料工程 | 0.07 | 0.22 |

比方說，當數量的資料分析-標準層是使用時，0.4 單位中扣除預先購買的 Databricks 認可單位。

在購買之前，會計算不同的工作負載和層級所耗用的總 DBU 數量。 使用上述的比例 DBCU 正規化，然後再執行下一步一或三年來預估的總使用量。

## <a name="purchase-databricks-commit-units"></a>購買 Databricks 認可單位

您可以購買 Databricks 計劃[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)。 若要購買保留的容量，您必須至少一個 enterprise 訂用帳戶的擁有者角色。

- 目前，預先購買只適用於 Enterprise 合約客戶。
- 您必須在至少一個企業訂用帳戶的擁有者角色。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用**新增保留執行個體**。 或者，如果該設定已停用，您必須是 EA 管理員的訂用帳戶。

**若要購買：**

1. 移至 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)。
1. 選取一個訂用帳戶。 使用**訂用帳戶**清單以選取用來針對所保留的容量付費的訂用帳戶。 訂用帳戶的付款方法會負責的保留容量的前期成本。 費用是從註冊的承諾用量金額餘額扣除或作為超額部分收費。
1. 選取範圍。 使用**範圍**清單來選取訂用帳戶範圍：
    - **單一資源群組範圍**— 在選取的資源群組中的比對資源套用保留折扣。
    - **單一訂用帳戶範圍**— 保留折扣會套用至選取的訂用帳戶中比對資源。
    - **共用範圍**— 會保留折扣套用至對應的計費內容中的合格訂用帳戶中的資源。 Enterprise 合約客戶，計費內容會是註冊。
1. 選取您想要購買，並完成購買程序的幾個 Azure Databricks 認可單位。


![在 Azure 入口網站中顯示 Azure Databricks 採購單的範例](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>變更領域和擁有權

您可以進行下列類型的變更，到購買後的保留項目：

- 更新保留範圍
- 以角色為基礎的存取

您無法分割或合併 Databricks 認可單元預先購買。 如需管理保留的詳細資訊，請參閱 <<c0> [ 購買後管理保留項目](billing-manage-reserved-vm-instance.md)。

## <a name="cancellations-and-exchanges"></a>取消和交換

Databricks 預購方案不支援取消和交換。 所有購買項目是最後一個項目。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
  - [了解 Azure Databricks 預購 DBCU 折扣如何套用](billing-reservation-discount-databricks.md)
  - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
