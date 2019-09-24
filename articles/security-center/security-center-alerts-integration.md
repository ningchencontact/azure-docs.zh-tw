---
title: 整合 Azure 資訊安全中心中的 Azure 安全性產品 |Microsoft Docs
description: 本主題提供已與 Azure 資訊安全中心整合的 Azure 安全性產品。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 6560db7e2f1cb363e0b8ca7af3a08f6babd9b36b
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202405"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Azure 安全性產品在 Azure 資訊安全中心中的整合

Azure 資訊安全中心提供您額外的 Microsoft 授權來與下列安全性產品搭配使用：

* [Azure WAF](#azure-waf)
* [Azure DDoS 保護](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

Web 應用程式的目標是利用常見已知弱點的惡意攻擊。 應用程式閘道 WAF 是根據「開啟 Web 應用程式安全性」專案中的核心規則集3.0 或2.2.9。 WAF 會自動更新以防止新的弱點, 而不需要額外的設定。 WAF 警示會串流處理到資訊安全中心。 如需 WAF 所產生警示的詳細資訊，請參閱[Web 應用程式防火牆 CRS 規則群組與規則](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)。

## Azure DDoS 保護<a name="azure-ddos"></a>

分散式阻斷服務 (DDoS) 攻擊已知容易執行。 它們已成為絕佳的安全性考慮，特別是當您將應用程式移至雲端時。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路觸達的任何端點。

Azure DDoS 保護，與應用程式設計最佳作法結合，可提供對抗 DDoS 攻擊的防禦措施。 DDoS 保護提供不同的服務層級。 如需詳細資訊, 請參閱[Azure DDoS 保護總覽](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

DDoS 保護標準層可降低下列攻擊類型的風險：

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**偵測到體積型攻擊**|這種攻擊的目標是要以大量看似合法的流量淹沒網路層。 它包括 UDP 洪水攻擊、放大洪水攻擊和其他詐騙封包洪水攻擊。 DDoS Protection Standard 會自動吸收並清除這些潛在的多 gb 攻擊，藉此降低其全域網路規模。|
|**偵測到通訊協定攻擊**|這些攻擊會藉由利用第3層和第4層通訊協定堆疊中的弱點，而使目標變得無法存取。 其中包括 SYN 洪水攻擊、反映攻擊和其他通訊協定攻擊。 DDoS 保護標準層可透過與用戶端互動來區別惡意與合法流量並封鎖惡意流量，以降低這些攻擊的風險。|
|**偵測到資源 (應用程式) 層攻擊**|這些攻擊會以 web 應用程式封包為目標, 以中斷主機之間的資料傳輸。 攻擊包括 HTTP 通訊協定違規、SQL 插入、跨網站指令碼和其他第 7 層攻擊。 使用 Azure 應用程式的閘道 WAF，搭配 DDoS 保護標準來防禦這些攻擊。 Azure Marketplace 也提供協力廠商 WAF 供應專案。|
