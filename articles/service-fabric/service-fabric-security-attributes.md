---
title: Azure Service Fabric 的安全性屬性
description: 評估 Azure Service Fabric 的安全性屬性檢查清單
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443862"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Azure Service Fabric 的安全性屬性

本文記載 Azure Service Fabric 內建的安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)| 是 | 客戶擁有叢集, 以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 |  |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶擁有叢集, 以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| 是 |  |
| 網路隔離和防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 強制通道支援| 是 | Azure 網路提供強制通道功能。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 使用 Azure 監視支援和協力廠商支援。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| Authorization| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和審核| N/A | 客戶擁有叢集。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | |