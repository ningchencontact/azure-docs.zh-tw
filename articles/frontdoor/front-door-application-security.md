---
title: Azure Front Door Service - 應用程式層安全性 | Microsoft Docs
description: 此文章可協助您了解 Azure Front Door Service 如何保護應用程式後端
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: c7b99548e2fe1ad0c1cab39953e28a97e7ebff4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193912"
---
# <a name="application-layer-security-with-front-door"></a>Front Door 的應用程式層安全性
Azure Front Door Service 提供的 Web 應用程式保護功能可保護 Web 應用程式免於受到網路攻擊與常見網路弱點惡意探索，例如 SQL 插入或跨網站指令碼 (XSS)。 針對 http(s) 前端啟用，Front Door 的應用程式層安全性散布全球各地且永遠開啟，可在遠離您後端的 Azure 網路邊緣遏止惡意攻擊。 Front Door 可提高安全性並將效能最佳化，向您的終端使用者提供快速又安全的 Web 體驗。

## <a name="application-protection"></a>應用程式保護
Front Door 的應用程式保護是在全球各地的每個邊緣環境配合應用程式設定的，而且會自動封鎖非 http(s) 流量觸達您的 Web 應用程式。 我們的多租用戶分散式架構可提供大規模全球保護，卻不必犧牲效能。 針對 http(s) 工作負載，Front Door 的 Web 應用程式保護服務可為自訂規則、對常見攻擊預先設定的規則集提供豐富的規則引擎，並詳細記錄符合規則的所有要求。 可彈性使用的支援動作包括允許、封鎖或僅記錄。

## <a name="custom-access-control-rules"></a>自訂存取控制規則
- **IP 允許清單和封鎖清單：** 您可以設定自訂規則來控制存取權的用戶端 IP 位址的清單為基礎的 web 應用程式。 支援 IP v4 與 IP v6 兩者
- **地理型的存取控制：** 您可以設定自訂規則來控制存取權的用戶端 IP 是來自的國家/地區程式碼為基礎的 web 應用程式
- **篩選的 HTTP 參數：** 您可以設定根據比對 http 要求參數包括標頭、 URL 和查詢字串的自訂存取規則

## <a name="azure-managed-rules"></a>Azure 受控規則
- 預設會啟用針對最常見 OWASP 弱點預先設定的規則集。 預覽期間的規則集包含 sqli 與 xss 要求檢查。 未來將會新增其他規則。 您可以從選擇只記錄動作開始，以驗證為您的應用程式預先設定的規則如預期般運作 

## <a name="rate-limiting"></a>速率限制
- 速率控制規則是要限制來自任何用戶端 IP 的異常高流量。  您可以設定用戶端 IP 在一分鐘的時間範圍內允許的 Web 要求數目閾值。

## <a name="centralized-protection-policy"></a>集中式保護原則
- 您可以定義數個保護規則，並依優先順序將其增至「原則」。 自訂規則的優先順序高於受控規則集，以允許例外。 單一原則會與您的 Web 應用程式相關聯。  相同的 Web 應用程式保護原則會複寫到所有位置的所有 Edge Server ，確保所有區域都有一致的安全性原則

## <a name="configuration"></a>組態
- 在預覽期間，您可以使用 REST API、PowerShell 或 CLI 來建立及部署 Front Door 的應用程式保護規則與原則。 在服務公開推出之前，支援以入口網站存取。 


## <a name="monitoring"></a>監視
Front Door 可讓您使用與 Azure 監視器整合以追蹤警示並輕鬆監視趨勢的即時計量，來監視對 Web 應用程式的攻擊。

## <a name="pricing"></a>價格
預覽期間免費提供 Front Door 的應用程式層安全性。


## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
