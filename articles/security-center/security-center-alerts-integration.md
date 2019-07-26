---
title: 整合 Azure 資訊安全中心中的 Azure 安全性產品 |Microsoft Docs
description: 本主題提供已與 Azure 資訊安全中心整合的 Azure 安全性產品。
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
ms.author: v-mohabe
ms.openlocfilehash: 847748d1c56221119d8f74a2aee716ee08448e28
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335809"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Azure 安全性產品在 Azure 資訊安全中心中的整合

資訊安全中心為客戶提供其他 Microsoft 授權, 讓他們的發現能夠資訊安全中心, 並以合併的方式進行觀看。

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

Web 應用程式的目標是利用常見已知弱點的惡意攻擊。 應用程式閘道 WAF 是以核心規則集 (CRS) 3.0 或來自 Open Web Application Security Project (OWASP) 的2.2.9 為基礎。 WAF 會自動更新以防止新的弱點, 而不需要額外的設定。 WAF 所產生的警示會串流處理到資訊安全中心。 如需 WAF 所產生警示的詳細資訊, 請參閱這[篇文章](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)。

## Azure DDoS <a name="azure-ddos"></a>

分散式阻斷服務 (DDoS) 攻擊已知容易執行。 因此, 對於將應用程式移至雲端的客戶而言, 他們已成為絕佳的安全性考慮。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路觸達的任何端點。

Azure DDoS 保護與應用程式設計最佳作法結合, 可提供對抗 DDoS 攻擊的防線。 Azure DDoS 保護提供不同的服務層級。 如需詳細資訊, 請參閱[Azure DDoS 保護總覽](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

DDoS 保護標準層可降低下列攻擊類型的風險：

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**偵測到體積型攻擊**|這種攻擊的目標是要以大量看似合法的流量淹沒網路層。 它包括 UDP 洪水攻擊、放大洪水攻擊和其他詐騙封包洪水攻擊。 DDoS 保護標準層可降低這些潛在多 GB 攻擊的風險，方法是自動使用 Azure 的全球網路規模來快吸並清除這些攻擊。|
|**偵測到通訊協定攻擊**|這些攻擊會藉由利用第3層和第4層通訊協定堆疊中的弱點, 來呈現無法存取的目標。 它包括 SYN 洪水攻擊、反映攻擊和其他通訊協定攻擊。 DDoS 保護標準層可透過與用戶端互動來區別惡意與合法流量並封鎖惡意流量，以降低這些攻擊的風險。|
|**偵測到資源 (應用程式) 層攻擊**|這些攻擊會以 web 應用程式封包為目標, 以中斷主機之間的資料傳輸。 攻擊包括 HTTP 通訊協定違規、SQL 插入、跨網站指令碼和其他第 7 層攻擊。 使用 Azure 應用程式閘道 web 應用程式防火牆搭配 DDoS 保護標準, 以防禦這些攻擊。 Azure Marketplace 也提供協力廠商 web 應用程式防火牆供應專案。|
