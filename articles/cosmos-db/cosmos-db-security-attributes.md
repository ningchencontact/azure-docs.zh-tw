---
title: 適用於 Azure Cosmos DB 的安全性屬性
description: 評估 Azure Cosmos DB 的安全性屬性的檢查清單
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480474"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的安全性屬性

這篇文章說明 Azure Cosmos DB 內建的一般安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 靜態加密 （例如伺服器端加密，使用客戶管理的金鑰，伺服器端加密和其他加密功能） | 是 | 所有 Cosmos DB 資料庫和備份預設會經過都加密;請參閱[Azure Cosmos DB 中的資料加密](database-encryption-at-rest.md)。 不支援使用客戶管理金鑰的伺服器端加密。 |
| 傳輸中加密 （例如 ExpressRoute 加密，Vnet 加密和 VNet 對 VNet 加密中）| 是 | Azure Cosmos DB 的所有資料都會進行傳輸都加密。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |  |
| 資料行層級加密 (Azure 資料服務)| 是 | 只有在資料表 API Premium。 並非所有的 Api 支援這項功能。 請參閱[Azure Cosmos DB 簡介：資料表 API](table-introduction.md)。 |
| API 呼叫加密| 是 | 所有連線到 Azure Cosmos DB 都支援 HTTPS。 Azure Cosmos DB 也支援 TLS 1.2 連線，但這不會還強制執行。 如果客戶是由較低的層級 TLS 端關閉，他們可以確保連線到 Cosmos DB。  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| vNET 插入支援| 是 | VNet 服務端點，您可以設定以允許存取只能從虛擬網路 (VNet) 的特定子網路的 Azure Cosmos DB 帳戶。 您也可以結合 VNet 存取的防火牆規則。  請參閱[存取 Azure Cosmos DB，從虛擬網路](vnet-service-endpoint.md)。 |
| 網路隔離和 Firewalling 支援| 是 | 透過防火牆支援，您可以設定您的 Azure Cosmos 帳戶，以允許存取只能從一組核准的 IP 位址、 IP 位址範圍，及/或雲端服務。 請參閱[Azure Cosmos DB 中設定的 IP 防火牆](how-to-configure-firewall.md)。|
| 支援強制通道 | 是 | 可以設定用戶端上的虛擬機器的所在位置的 VNET。   |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 記錄傳送至 Azure Cosmos DB 的所有要求。 [Azure 監視](../azure-monitor/overview.md)，支援 Azure 計量、 Azure 稽核記錄。  您可以記錄對應到資料平面要求查詢執行階段統計資料、 查詢文字的資訊，請要求 MongoDB。 您也可以設定警示。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 是資料庫層級的帳戶;在資料平面層級，Cosmos DB 會使用資源權杖和金鑰的存取。 |
| Authorization| 是 | 支援在具有主要金鑰 （主要和次要） 的 Azure Cosmos 帳戶和資源權杖。 您可以取得讀取/寫入或讀取資料與主要金鑰的權限。 資源權杖允許有限的時間存取資源，例如文件和容器。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核| 是 | Azure 活動記錄檔以取得帳戶層級的作業，例如防火牆、 Vnet、 存取金鑰，與 IAM。 |
| 資料平面記錄和稽核 | 是 | 診斷監視這類記錄容器層級的作業建立的容器，佈建輸送量，編製索引原則，以及在文件上的 CRUD 作業。 |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 否  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>適用於 Cosmos DB 的額外的安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 跨原始資源共用 (CORS) | 是 | 請參閱[設定跨原始資源共用 (CORS)](how-to-configure-cross-origin-resource-sharing.md)。 |
