---
title: 包含檔案
description: 包含檔案
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 314f29a1135e355597e890b72ef3c0b7372194e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227016"
---
### <a name="sql-databases"></a>SQL Database

|  |  |
|---------|---------|
| [允許的 SQL DB SKU](../articles/governance/policy/samples/allowed-sql-db-skus.md) | 要求 SQL 資料庫使用已核准的 SKU。 您需指定允許的 SKU 識別碼陣列或允許的 SKU 名稱陣列。 |
| [稽核 DB 層級威脅偵測設定](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | 稽核 SQL 資料庫安全性警示原則是否未設定成指定的狀態。 您需指定一個指出已啟用或停用威脅偵測的值。  |
| [稽核 SQL Database 加密](../articles/governance/policy/samples/sql-database-encryption-audit.md) | 稽核 SQL 資料庫是否未啟用透明資料加密。 |
| [稽核 SQL DB 層級稽核設定](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | 稽核 SQL 資料庫稽核設定是否與指定的設定不符。 您需指定一個指出應啟用或停用稽核設定的值。  |