---
title: 適用於 Azure Service Fabric 常見的安全性屬性
description: 用來評估 Azure Service Fabric 的常見安全性屬性檢查清單
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003062"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Azure Service Fabric 的安全性屬性

這篇文章說明 Azure Service Fabric 內建的安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 有 | 客戶擁有叢集和虛擬機器擴展集上建立叢集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 有 |  |
| 加密金鑰處理 （CMK、 BYOK）| 有 | 客戶擁有叢集和虛擬機器擴展集上建立叢集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 資料行層級加密 (Azure Data Services)| N/A |  |
| API 呼叫加密| 有 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 有 |  |
| VNet 插入支援| 有 |  |
| 網路隔離，而且防火牆支援| 有 | 使用網路安全性群組 (NSG)。 |
| 強制通道的支援| 有 | Azure 網路提供強制通道功能。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 有 | 使用 Azure 監視支援和第三方支援。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 有 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 有 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 有 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄與稽核| N/A | 客戶擁有叢集。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 有 | |