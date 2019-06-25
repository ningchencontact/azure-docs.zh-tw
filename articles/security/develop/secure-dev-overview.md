---
title: 安全開發在 Microsoft Azure 上的最佳作法
description: 最佳作法，協助您開發更安全的程式碼並部署在雲端中更安全的應用程式。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144460"
---
# <a name="secure-development-best-practices-on-azure"></a>安全開發在 Azure 上的最佳作法
這一系列的文章提供的安全性活動和您開發的雲端應用程式時要考量的控制項。 涵蓋的 Microsoft 安全性開發生命週期 (SDL) 和安全性問題和需要考量的生命週期的每個階段的概念的階段。 目標是為了協助您定義活動和可用在生命週期的每個階段來設計、 開發及部署更安全的應用程式的 Azure 服務。

在文章中的建議來自從我們的經驗，使用 Azure 資訊安全和我們的客戶體驗。 可用於這些文件做為參考您應該考慮在您的開發專案的特定階段期間，但我們建議您也閱讀所有文章，從開始到結束至少一次。 閱讀所有文章將介紹您可能會遺失您的專案的早期階段中的概念。 實作這些概念，您在發行產品之前，可協助您建置安全軟體、 處理安全性合規性需求，並降低開發成本。

這些文章要作為軟體設計人員，開發人員的資源和測試人員所有層級的人員建置和部署安全的 Azure 應用程式。

## <a name="overview"></a>概觀

安全性是任何應用程式，最重要的層面之一，並不是簡單的方法是，若要取得權限。 幸運的是，Azure 提供許多服務，可協助您保護雲端中的應用程式。 這些文件來解決活動和 Azure 服務，您可以實作您軟體開發生命週期，可協助您開發更安全的程式碼並部署更安全的應用程式在雲端中的每個階段。

## <a name="security-development-lifecycle"></a>安全性開發生命週期

下列最佳作法進行安全的軟體開發，您需要將安全性整合至軟體開發生命週期，從維護模式，不論專案方法的需求分析的每個階段 ([瀑布](https://en.wikipedia.org/wiki/Waterfall_model)， [agile](https://en.wikipedia.org/wiki/Agile_software_development)，或[DevOps](https://en.wikipedia.org/wiki/DevOps))。 在網路喚醒的高設定檔資料外洩和操作的安全性弱點的入侵，更多開發人員都了解安全性需要處理整個開發程序。

修正問題的更新版本中程式開發生命週期中，修正將更多成本。 安全性問題也不例外。 如果您在軟體開發的早期階段中忽略的安全性問題，會遵循每個階段可能會繼承的漏洞的一個階段。 您在最終產品將累積多個安全性問題和缺口的可能性。 安全性開發生命週期的每個階段內建可協助您及早發現問題，它可協助您降低開發成本。

我們遵循 Microsoft 的階段[安全性開發生命週期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)導入活動和可用來滿足安全軟體開發實務生命週期的每個階段中的 Azure 服務。

SDL 階段是：

  - 訓練
  - 需求
  - 設計
  - 實作
  - 验证
  - 版本
  - Response

![安全性開發生命週期](./media/secure-dev-overview/01-sdl-phase.png)

這些文章中我們 SDL 階段群設計、 開發及部署。

## <a name="engage-your-organizations-security-team"></a>與您組織的安全性團隊合作

您的組織可能會有正式的應用程式安全性計劃可協助您進行從開始到結束期間開發生命週期的安全性活動。 如果您的組織有安全性與合規性的小組，請務必開始開發您的應用程式之前，請連絡他們。 要求他們 SDL 的每個階段是否錯過任何工作。

我們了解許多讀者可能沒有安全性或合規性小組交流。 這些文章可協助引導您在 安全性問題和您需要考慮 SDL 的每個階段的決策。

## <a name="resources"></a>資源

若要深入了解如何開發安全應用程式，並協助保護您在 Azure 上的應用程式，請使用下列資源：

[Microsoft 安全性開發生命週期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL 是協助開發人員建置更安全軟體的 microsoft 軟體開發程序。 它可協助您解決安全性合規性需求，同時降低開發成本。

[開啟 Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP 是一個線上社群，會產生免費提供的發行項、 方法、 文件、 工具和技術的 web 應用程式安全性 欄位中。

[將左推送老闆等](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca)– 一系列的線上文件，概述不同類型的應用程式開發人員應該完成建立更安全的程式碼的安全性活動。

[Microsoft 身分識別平台](https://docs.microsoft.com/azure/active-directory/develop/)– Microsoft 身分識別平台是 Azure AD 身分識別服務和開發人員平台的進化。 它是一個功能完整的平台，包含驗證服務、 開放原始碼程式庫、 應用程式註冊和組態、 完整的開發人員文件、 程式碼範例和其他開發人員內容。 Microsoft 身分識別平台支援業界標準通訊協定，例如 OAuth 2.0 和 OpenID Connect。

[Azure 解決方案的安全性最佳作法](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)– 您設計、 部署及管理雲端解決方案，藉由使用 Azure 時所要使用的安全性最佳作法的集合。 這份文件是適用於 IT 專業人員的資源。 其中可包括建置和部署安全 Azure 解決方案的設計人員、架構設計師、開發人員和測試人員。

[安全性與在 Azure 上的合規性藍圖](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview)– Azure 安全性與合規性藍圖是資源，幫助您建置並啟動遵守嚴格的規範及標準的雲端架構應用程式。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議的安全性控制和活動，可協助您設計、 開發和部署安全的應用程式。

- [設計安全的應用程式](secure-design.md)
- [開發安全應用程式](secure-develop.md)
- [部署安全的應用程式](secure-deploy.md)
