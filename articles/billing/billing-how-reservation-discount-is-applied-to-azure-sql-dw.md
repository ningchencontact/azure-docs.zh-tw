---
title: 保留折扣如何套用到 Azure SQL 資料倉儲 |Microsoft Docs
description: 了解如何保留折扣套用到 Azure SQL 資料倉儲，以協助節省您的成本。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918353"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>保留折扣如何套用到 Azure SQL 資料倉儲

您購買保留的 Azure SQL 資料倉儲容量之後，保留折扣會自動套用至存在於該區域中的資料倉儲。 保留折扣適用於 SQL 資料倉儲 cDWU 計量發出使用量。 儲存體和網路會以隨用隨付費率計費。

## <a name="reservation-discount-application"></a>保留折扣應用程式

SQL 資料倉儲保留的容量折扣會套用到每小時執行倉儲。 如果您沒有部署到 1 小時的倉儲，該小時的浪費掉的保留的容量。 它不會延續。

購買後，您所購買的保留項目會對應至 SQL 資料倉儲發出在任何時間點的時間執行倉儲的使用量。 如果您關閉某些倉儲時，然後保留折扣會自動將套用至其他比對的倉儲。

如不執行完整的一小時的倉儲，此保留項目會自動套用到其他相符的執行個體在該小時內。

## <a name="discount-examples"></a>折扣範例

下列範例會示範根據部署而定，SQL 資料倉儲保留的容量折扣的套用方式。

- **範例 1**:您購買 100 cDWU 保留容量的 5 個單位。 您會執行不足一小時的 DW1500c SQL 資料倉儲執行個體。 在此情況下，使用方式，就會發出的 15 個單位的 100 cDWU 使用量。 保留折扣適用於您所使用的 5 個單位。 您需要使用剩餘的 10 個單位，您所使用的 100 cDWU 使用量的隨用隨付費率付費。

- **範例 2：** 您購買 100 cDWU 保留容量的 5 個單位。 您會執行不足一小時的兩個 DW100c SQL 資料倉儲執行個體。 在此情況下，1 個 100 cDWU 使用量單位會發出兩個的使用量事件。 這兩個使用量事件取得保留的容量折扣。 100 cDWU 保留容量的剩餘的 3 個單位會浪費並不會延續供日後使用。

- **範例 3**:您購買 100 cDWU 保留容量的 1 個單位。 您執行兩個 DW100c SQL 資料倉儲執行個體。 每個執行 30 分鐘。 在此案例中，這兩個使用量事件取得保留的容量折扣。 沒有使用方式是使用隨用隨付的費率來收費。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

- 如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
- [檢視保留交易](billing-view-reservations.md)
- [取得保留交易和透過 API 使用量](billing-reservation-apis.md)
- [管理保留項目](billing-manage-reserved-vm-instance.md)
