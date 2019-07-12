---
title: Azure 安全性產品的資訊安全中心整合 |Microsoft Docs
description: 本主題會提供已使用 Azure 資訊安全中心整合的 Azure 安全性產品。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571734"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>與 ASC 的 Azure 安全性產品的資訊安全中心整合

資訊安全中心提供上架的其他 Microsoft 授權的客戶小組的發現到資訊安全中心，並檢視合併的方式。

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

利用常見已知的弱點的惡意攻擊會越來越多的目標 web 應用程式。 應用程式閘道 WAF 是以基礎上核心規則集 (CRS) 3.0 或 2.2.9 從 Open Web Application Security Project (OWASP)。 WAF 會自動更新，以防止新的弱點，不需要的其他設定。 WAF 所產生的警示會串流處理到資訊安全中心。 如需有關產生 WAF 之警示的詳細資訊，請參閱此[文章](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)。

## Azure DDoS <a name="azure-ddos"></a>

分散式的阻絕服務 (DDoS) 攻擊已知很容易執行。 因此，它們已成為要移動到雲端應用程式的客戶有絕佳的安全性考量。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路連接的任何端點。

Azure DDoS 保護，結合應用程式設計最佳做法，提供 DDoS 攻擊的防禦。 Azure DDoS protection 會提供不同服務層。 如需詳細資訊，請參閱 < [Azure DDoS 保護概觀](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

DDoS 保護標準層可降低下列攻擊類型的風險：

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**偵測到的體積型攻擊**|此攻擊的目標是流量的要填滿網路層，以大量看似合法。 它包括 UDP 洪水攻擊、放大洪水攻擊和其他詐騙封包洪水攻擊。 DDoS 保護標準層可降低這些潛在多 GB 攻擊的風險，方法是自動使用 Azure 的全球網路規模來快吸並清除這些攻擊。|
|**偵測到的通訊協定攻擊**|這些攻擊所呈現的目標無法存取，利用第 3 層中的弱點和圖層的 4 個通訊協定堆疊。 它包括 SYN 洪水攻擊、反映攻擊和其他通訊協定攻擊。 DDoS 保護標準層可透過與用戶端互動來區別惡意與合法流量並封鎖惡意流量，以降低這些攻擊的風險。|
|**偵測到的資源 （應用程式） 層攻擊**|這些攻擊會鎖定 web 應用程式的封包，以中斷主機之間的資料傳輸。 攻擊包括 HTTP 通訊協定違規、SQL 插入、跨網站指令碼和其他第 7 層攻擊。 使用 Azure 應用程式閘道 web 應用程式防火牆，DDoS Protection Standard，來防範這些攻擊。 另外還有第三方 web 應用程式防火牆供應項目可在 Azure Marketplace 中。|
