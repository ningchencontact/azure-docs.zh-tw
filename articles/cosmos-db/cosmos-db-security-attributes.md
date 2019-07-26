---
title: Azure Cosmos DB 的安全性屬性
description: 評估 Azure Cosmos DB 的安全性屬性檢查清單
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 50711f8675e1b8aca6b9f90925ea921d22020ddd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442561"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Azure Cosmos DB 的安全性屬性

本文記載 Azure Cosmos DB 內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 預設會加密所有 Cosmos DB 資料庫和備份;請參閱[Azure Cosmos DB 中的資料加密](database-encryption-at-rest.md)。 不支援使用客戶管理的金鑰進行伺服器端加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | 所有 Azure Cosmos DB 資料都會在傳輸時加密。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |  |
| 資料行層級加密 (Azure 資料服務)| 是 | 僅適用于資料表 API Premium。 並非所有 Api 都支援這項功能。 請[參閱 Azure Cosmos DB 簡介:資料表 API](table-introduction.md)。 |
| API 呼叫加密| 是 | Azure Cosmos DB 的所有連接都支援 HTTPS。 Azure Cosmos DB 也支援 TLS 1.2 連線, 但尚未強制執行。 如果客戶在其端關閉了較低層級的 TLS, 則可以確保連線到 Cosmos DB。  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| 是 | 使用 VNet 服務端點, 您可以設定 Azure Cosmos DB 帳戶, 只允許從虛擬網路 (VNet) 的特定子網進行存取。 您也可以結合 VNet 存取與防火牆規則。  請參閱[從虛擬網路存取 Azure Cosmos DB](VNet-service-endpoint.md)。 |
| 網路隔離和防火牆支援| 是 | 有了防火牆支援, 您可以設定 Azure Cosmos 帳戶, 只允許來自一組已核准的 IP 位址、IP 位址和/或雲端服務的某個範圍的存取權。 請參閱[在 Azure Cosmos DB 中設定 IP 防火牆](how-to-configure-firewall.md)。|
| 強制通道支援| 是 | 可以在虛擬機器所在的 VNet 上, 于用戶端設定。   |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 所有傳送至 Azure Cosmos DB 的要求都會被記錄下來。 支援[Azure 監視](../azure-monitor/overview.md)、azure 計量、Azure Audit 記錄。  您可以記錄對應于資料平面要求、查詢執行時間統計資料、查詢文字、MongoDB 要求的資訊。 您也可以設定警示。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 是, 在資料庫帳戶層級;在資料平面層級, Cosmos DB 會使用資源權杖和金鑰存取。 |
| Authorization| 是 | 在具有主要金鑰 (主要和次要) 和資源權杖的 Azure Cosmos 帳戶上受到支援。 您可以使用主要金鑰來取得資料的讀取/寫入或唯讀存取權。 資源權杖允許限時存取檔和容器之類的資源。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 適用于帳戶層級作業 (例如防火牆、Vnet、金鑰存取和 IAM) 的 Azure 活動記錄。 |
| 資料平面記錄和審核 | 是 | 容器層級作業的診斷監視記錄, 例如建立容器、布建輸送量、編制索引原則, 以及檔上的 CRUD 作業。 |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 否  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB 的其他安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 跨原始來源資源分享 (CORS) | 是 | 請參閱[設定跨原始來源資源分享 (CORS)](how-to-configure-cross-origin-resource-sharing.md)。 |
