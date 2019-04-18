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
ms.openlocfilehash: f6db40a35c289fa0870ac4c9e5e55b773c84f4f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679222"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>適用於 Azure Service Fabric 常見的安全性屬性

Azure 服務的各個層面都整合了安全性。 這篇文章說明 Azure Service Fabric 內建的一般安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 客戶擁有叢集和虛擬機器擴展集上建立叢集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 |  |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶擁有叢集和虛擬機器擴展集上建立叢集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| vNET 插入支援| 是 |  |
| 網路隔離/防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 支援強制通道 | 是 | Azure 網路提供強制通道功能。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 使用 Azure 監視支援和第三方支援。 |

## <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 存取管理 - 授權| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和稽核| N/A | 客戶擁有叢集。  |

## <a name="configuration-management"></a>組態管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 服務組態會使用 Azure 部署進行版本控制和部署。 程式碼 (應用程式和執行階段) 會使用 Azure Build 進行版本控制。