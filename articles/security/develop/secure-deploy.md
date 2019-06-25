---
title: 部署 Microsoft Azure 上的安全應用程式
description: 這篇文章討論您的 web 應用程式專案的版本和回應階段納入考量的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144447"
---
# <a name="deploy-secure-applications-on-azure"></a>部署在 Azure 上的安全應用程式
這篇文章中我們將會呈現安全性活動和您部署的雲端應用程式時要考量的控制項。 安全性問題和概念，請考慮在 Microsoft 的版本和回應階段[安全性開發生命週期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)涵蓋。 目標是為了協助您定義活動和可用來部署更安全的應用程式的 Azure 服務。

此文章涵蓋下列 SDL 階段：

- 版本
- Response

## <a name="release"></a>版本
發行階段的重點準備公開發行的專案。
這包括規劃如何有效地執行發行後服務工作，並解決可能會稍後發生的安全性弱點。

### <a name="check-your-applications-performance-before-you-launch"></a>檢查您的應用程式效能，然後再啟動

啟動它，或將更新部署至生產環境之前，請檢查您的應用程式效能。 執行雲端[負載測試](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)使用 Visual Studio，您的應用程式中找出效能問題，改善部署品質，請確定您的應用程式永遠是最新或可用，並且您的應用程式可以處理流量您的上市。

### <a name="install-a-web-application-firewall"></a>安裝 Web 應用程式防火牆

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 這些攻擊中最常見的是 SQL 資料隱碼攻擊和跨網站指令碼攻擊。 防止應用程式程式碼中的這些攻擊很有挑戰性。 它可能會需要嚴格的維護、 修補和監視的應用程式拓撲的多個層級。 集中式的 WAF 可協助簡化安全性管理。 WAF 方案還可因應由修補已知的弱點的中央位置，相較於保護每個個別的 web 應用程式的安全性威脅。

[Azure 應用程式閘道 WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview)提供集中式的保護的 web 應用程式免於遭遇常見攻擊和弱點。 WAF 根據規則[OWASP 核心規則集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)3.0 或 2.2.9。

### <a name="create-an-incident-response-plan"></a>建立事件因應計劃

準備事件因應計劃，請務必以協助您解決可能會出現一段時間的新威脅。 準備事件因應計劃，其中包括用來識別適當的安全性緊急連絡人，以及建立服務方案從組織中的其他群組繼承的程式碼和授權的協力廠商程式碼的安全性。

### <a name="conduct-a-final-security-review"></a>進行最終安全性檢閱

刻意檢閱所執行的所有安全性活動，可協助確保軟體發行或應用程式的完備性。 最終安全性檢閱 (FSR) 通常會包含檢查威脅分析模型、 工具輸出，以及效能與品質閘門並在需求階段中所定義的 bug 長條。

### <a name="certify-release-and-archive"></a>發行和封存認證

認證的軟體之前的版本可協助確保符合安全性與隱私權需求。 封存相關的所有資料都是不可或缺的執行發行後服務工作。 封存也有助於降低長期持續性的軟體工程相關聯的成本。

## <a name="response"></a>Response
回應後發行階段著重於開發團隊能且可適當回應新興的軟體威脅與弱點的任何報告。

### <a name="execute-the-incident-response-plan"></a>執行事件回應計劃

無法實作事件回應計劃在發行階段所制定是可以幫助客戶防範軟體安全性或隱私權的弱點，才會出現。

### <a name="monitor-application-performance"></a>監視應用程式的效能

持續監視您的應用程式的可能部署之後，可協助您偵測效能問題，以及安全性弱點。
協助應用程式監視的 azure 服務有：

  - Azure Application Insights
  - Azure 資訊安全中心

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多個平台上的 Web 開發人員所適用的可延伸應用程式效能管理 (APM) 服務。 您可以使用它來監視即時 Web 應用程式。 Application Insights 會自動偵測效能異常。 其中包括強大的分析工具，可協助您診斷問題，以及了解使用者實際如何運用您的應用程式。 它是設計來協助您持續改善效能和可用性。

#### <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)可協助您預防、 偵測及回應威脅，更清楚 （和控制） 您的 Azure 資源，包括 web 應用程式的安全性。 Azure 資訊安全中心可協助偵測可能的輕微的威脅。 它適用於各種不同的安全性解決方案。

資訊安全中心的免費層僅為 Azure 資源提供有限的安全性。 [資訊安全中心標準層](https://docs.microsoft.com/azure/security-center/security-center-onboarding)擴充這些功能，可在內部部署資源和其他雲端。
資訊安全中心標準可協助您：

  - 尋找和修正安全性弱點。
  - 適用於存取和應用程式控制來封鎖惡意活動。
  - 使用分析和情報來偵測威脅。
  - 在遭受攻擊時迅速回應。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議的安全性控制和活動，可協助您設計和開發安全應用程式。

- [設計安全的應用程式](secure-design.md)
- [開發安全應用程式](secure-develop.md)
