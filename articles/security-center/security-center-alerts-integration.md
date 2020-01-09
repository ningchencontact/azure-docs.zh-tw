---
title: Azure 安全性產品在 Azure 資訊安全中心中的威脅偵測
description: 本主題提供 Azure 資訊安全中心可提供威脅偵測的 Azure 安全性產品
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 1ea207f0ba09e0637a08632d5c56591fd1335b22
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665725"
---
# <a name="threat-detection-for-azure-waf-and-azure-ddos-protection"></a>Azure WAF 和 Azure DDoS 保護的威脅偵測

Azure 資訊安全中心可以為下列 Azure 安全性產品提供威脅偵測（每項產品都需要個別的授權）：

* [Azure WAF](#azure-waf)
* [Azure DDoS 保護](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

Web 應用程式已逐漸成為利用常見已知弱點進行惡意攻擊的目標。 應用程式閘道 WAF 是根據「開啟 Web 應用程式安全性」專案中的核心規則集3.0 或2.2.9。 WAF 會自動更新以防止新的弱點，而不需要額外的設定。 WAF 警示會串流處理到資訊安全中心。 如需 WAF 所產生警示的詳細資訊，請參閱[Web 應用程式防火牆 CRS 規則群組與規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。

## Azure DDoS 保護<a name="azure-ddos"></a>

分散式阻斷服務（DDoS）攻擊已知容易執行。 它們已成為絕佳的安全性考慮，特別是當您將應用程式移至雲端時。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路觸達的任何端點。

Azure DDoS 保護，與應用程式設計最佳作法結合，可提供對抗 DDoS 攻擊的防禦措施。 DDoS 保護提供不同的服務層級。 如需詳細資訊，請參閱[Azure DDoS 保護總覽](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

如需 Azure DDoS 保護警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureddos)。