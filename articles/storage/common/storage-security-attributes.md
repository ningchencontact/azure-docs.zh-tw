---
title: Azure 儲存體的安全性屬性
description: 評估 Azure 儲存體的安全性屬性檢查清單
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 16ec2757955b53a8bfa73ba724100f7fa61d2867
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444309"
---
# <a name="security-attributes-for-azure-storage"></a>Azure 儲存體的安全性屬性

本文記載 Azure 儲存體內建的安全性屬性。 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)| 是 |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | 支援標準 HTTPS/TLS 機制。  使用者也可以在將資料傳送至服務之前, 將其加密。 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 請參閱[儲存體服務加密在 Azure Key Vault 中使用客戶管理的金鑰](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 |  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| N/A |  |
| 網路隔離和防火牆支援| 是 | |
| 強制通道支援| N/A |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 現在已可使用 Azure 監視器計量, 記錄開始預覽 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory、共用金鑰、共用存取權杖。 |
| Authorization| 是 | 透過 RBAC、POSIX Acl 和 SAS 權杖支援授權 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核 | 是 | Azure Resource Manager 活動記錄 |
| 資料平面記錄和審核| 是 | 服務診斷記錄, 以及開始預覽 Azure 監視器記錄  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過 Azure Resource Manager Api 支援資源提供者版本設定 |