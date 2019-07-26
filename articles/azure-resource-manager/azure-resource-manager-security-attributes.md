---
title: Azure Resource Manager 的安全性屬性
description: 評估 Azure Resource Manager 的安全性屬性檢查清單
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e3bfb79c54ff57adfa947f2dd0100f6c05c7af9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444159"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Azure Resource Manager 的安全性屬性

本文記載 Azure Resource Manager 內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)| 是 |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | HTTPS/TLS。 |
| 加密金鑰處理 (CMK、BYOK 等)| N/A | Azure Resource Manager 不會儲存任何客戶內容, 只會儲存控制資料。 |
| 資料行層級加密 (Azure 資料服務)| 是 | |
| API 呼叫加密| 是 | |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 否 |  |
| 強制通道支援| 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 否 | |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 以[Azure Active Directory](/azure/active-directory)為基礎。|
| Authorization| 是 | |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 活動記錄會公開您資源上執行的所有寫入作業 (PUT、POST、DELETE);請參閱[查看活動記錄以在資源上審核動作](resource-group-audit.md)。 |
| 資料平面記錄和審核| N/A | |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 |  |
