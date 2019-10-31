---
title: Azure API 管理開發人員入口網站總覽-Azure API 管理 |Microsoft Docs
description: 深入瞭解 API 管理中的開發人員入口網站。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: eb5e2c2e2eeb0f29eb74b3727ecf14d70d2381f6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176680"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理開發人員入口網站總覽

開發人員入口網站是一個自動產生、可完全自訂的網站，其中包含您 Api 的檔。 API 取用者可以在此探索您的 Api、瞭解如何使用它們、要求存取權，以及如何試用。

本文說明「API 管理」中的開發人員入口網站自我裝載和受管理版本之間的差異。 它也會說明它的架構，並提供常見問題的解答。

> [!IMPORTANT]
> [瞭解如何從預覽版本遷移至正式推出版本](#preview-to-ga)的開發人員入口網站。

![API Management developer portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>受控和自我裝載版本

您可以透過兩種方式來建立開發人員入口網站：

- **受控版本**-藉由編輯和自訂入口網站（內建于您的 API 管理實例中），並可透過 `<your-api-management-instance-name>.developer.azure-api.net`的 URL 存取。 請參閱[此檔文章](api-management-howto-developer-portal-customize.md)，以瞭解如何存取和自訂受管理的入口網站。
- **自我裝載版本**-藉由在 API 管理實例外部部署和自我裝載您的入口網站。 這種方法可讓您編輯入口網站的程式碼基底，並擴充提供的核心功能。 您也需要自行將入口網站升級至最新版本。 如需詳細資訊和指示，請參閱[GitHub 存放庫，並提供入口網站的原始程式碼][1]。 [受控版本的教學](api-management-howto-developer-portal-customize.md)課程會逐步解說入口網站的系統管理面板，此功能也會在自我裝載版本中提供。

## <a name="portal-architectural-concepts"></a>入口網站架構概念

入口網站元件可以邏輯方式分成兩個類別：程式*代碼*和*內容*。

程式*代碼*會保留在[GitHub 存放庫][1]中，並包含：

- Widget-代表視覺元素，並結合 HTML、JavaScript、樣式功能、設定和內容對應。 範例包括影像、文欄位落、表單、Api 清單等等。
- 樣式定義-指定 widget 如何設定樣式
- 引擎-這會從入口網站內容產生靜態網頁，並以 JavaScript 撰寫
- 視覺化編輯器-允許在瀏覽器內自訂和撰寫體驗

*內容*分為兩個子類別：*入口網站內容*和*API 管理內容*。

入口網站的*內容*專屬於入口網站，其中包括：

- 頁面-例如登陸頁面、API 教學課程、blog 文章
- 媒體-影像、動畫及其他檔案型內容
- 版面配置-範本，會與 URL 比對，並定義頁面的顯示方式
- 樣式-樣式定義的值，例如字型、色彩、框線
- 設定-例如 favicon、網站中繼資料

*入口網站內容*（媒體除外）會以 JSON 檔表示。

*Api 管理內容*包括 Api、作業、產品、訂用帳戶等實體。

入口網站是以[Paperbits 架構](https://paperbits.io/)的調整分支為基礎。 原始的 Paperbits 功能已擴充，可提供 API 管理特定的 widget （例如 Api 清單、產品清單），以及用於儲存和抓取內容的 API 管理服務連接器。

## <a name="faq"></a>常見問題

在本節中，我們會回答有關新開發人員入口網站的常見問題，這些是一般性質的問題。 如需自我裝載版本的特定問題，請參閱[GitHub 存放庫的 wiki 一節](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> 如何從入口網站的預覽版本進行遷移？

藉由使用開發人員入口網站的預覽版本，您可以在 API 管理服務中布建預覽內容。 在正式推出版本中，已大幅修改預設內容以獲得更佳的使用者體驗。 它也包含新的小工具。

如果您使用的是受控版本，請按一下 [**作業**] 功能表區段中的 [**重設內容**]，重設入口網站的內容。 確認此操作將會移除入口網站的所有內容，並布建新的預設內容。 入口網站的引擎已自動在您的 API 管理服務中更新。

![重設入口網站內容](media/api-management-howto-developer-portal/reset-content.png)

如果您使用自我裝載的版本，請使用 GitHub 存放庫中的 `scripts/cleanup.bat` 和 `scripts/generate.bat` 來移除現有的內容，並布建新的內容。 請務必事先從 GitHub 存放庫將入口網站的程式碼升級至最新版本。

如果您不想重設入口網站的內容，可以考慮在整個頁面中使用新的小工具。 現有 widget 已自動更新為最新版本。

如果您的入口網站是在公開上市後公告之後布建，則它應該已有新的預設內容功能。 您的端不需要採取任何動作。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>如何從舊的開發人員入口網站遷移至新的開發人員入口網站？

入口網站不相容，您必須手動遷移內容。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新的入口網站是否具有舊版入口網站的所有功能？

新的開發人員入口網站不支援*應用程式*和*問題*。 如果您在舊的入口網站中使用了*問題*，並在新的專案中需要它們，請在[專用的 GitHub 問題](https://github.com/Azure/api-management-developer-portal/issues/122)中張貼留言。

### <a name="has-the-old-portal-been-deprecated"></a>舊的入口網站已被取代嗎？

舊的開發人員和發行者入口網站現在是*舊版*的功能-他們只會接收安全性更新。 新功能只會在新的開發人員入口網站中執行。

舊版入口網站的淘汰將會分開宣告。 如果您有任何疑問、疑慮或意見，請[在專屬的 GitHub 問題中](https://github.com/Azure/api-management-developer-portal/issues/121)提出。

### <a name="how-can-i-automate-portal-deployments"></a>如何自動進行入口網站部署？

您可以透過 REST API 以程式設計方式存取和管理開發人員入口網站的內容，不論您使用的是受控或自我裝載版本。

此 API 記載于[GitHub 存放庫的 wiki 一節][2]。 它也可以用來自動化環境之間的入口網站內容遷移，例如從測試環境到生產環境。 您可以在 GitHub 上的[此檔文章中](https://aka.ms/apimdocs/migrateportal)深入瞭解此程式。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>入口網站是否支援 Azure Resource Manager 範本和（或）是否與 API 管理 DevOps 資源套件相容？

不會。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>我是否需要針對受管理的入口網站相依性啟用額外的 VNET 連線？

不會。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>我在使用互動式主控台時遇到 CORS 錯誤。 我該怎麼做？

互動式主控台會從瀏覽器提出用戶端 API 要求。 您可以藉由在您的 API 上新增[cors 原則](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)來解決 CORS 問題。 您可以手動指定所有參數（例如，原始檔 https://contoso.com) 或使用萬用字元 `*` 值。

## <a name="next-steps"></a>後續步驟

深入瞭解新的開發人員入口網站：

- [存取和自訂受管理的開發人員入口網站](api-management-howto-developer-portal-customize.md)
- [設定入口網站的自我裝載版本][2]

流覽其他資源：

- [具有原始程式碼的 GitHub 存放庫][1]
- [專案的公開藍圖][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects