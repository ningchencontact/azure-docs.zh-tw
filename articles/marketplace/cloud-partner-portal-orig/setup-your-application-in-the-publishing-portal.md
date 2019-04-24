---
title: 在發佈入口網站中設定您的應用程式 | Microsoft Docs
description: 關於如何在雲端發佈入口網站中設定應用程式的指示。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 8ac0fbb1c62e4162e1c4ad040365a16d055e4552
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60483160"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>在發佈入口網站中設定您的應用程式

您現已備妥可於發佈入口網站中設定您的應用程式。

## <a name="login-and-create-a-new-offer"></a>登入並建立新的供應項目

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 從左側導覽列，按一下 [+ 新增供應項目] 並選取 [Dynamics 365 for Customer Engagement]。

![選取新的供應項目](./media/CRMScreenShot14.png)

1. 隨即會開啟新供應項目的「編輯器」檢視，如此即已備妥可開始製作。

![新增供應項目畫面](./media/CRMScreenShot15.png)

1. 「編輯器」檢視左側中會出現需要填寫的「表單」。 每個「表單」都包含需要填寫的一組欄位。必要欄位會標有紅色星號 (\*)。

製作 Dynamics 365 for Customer Engagement 供應項目，有四個主要的表單

* 供應項目設定
* 技術資訊
* 店面詳細資料
* 連絡人

## <a name="fill-out-the-offer-settings-form"></a>填寫供應項目設定表單

供應項目設定表單是指定供應項目設定的基本表單。 下列說明各個不同欄位。

### <a name="offer-id"></a>供應項目識別碼

這是在發行者設定檔內供應項目的唯一識別碼。 此識別碼會出現在產品 URL 中。 此識別碼只能包含小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。

比方說，如果 「 發行者 」 **"contoso"** 發行者的供應項目 id 建立供應項目 **」 範例 WebApp"**，它會顯示在 AppSource 為"https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>發行者識別碼

此下拉式表單，可讓您選擇您想要在什麼發行者設定檔之下發佈此供應項目。 供應項目上架後，此欄位便會鎖住。

### <a name="name"></a>Name

您供應項目的顯示名稱。 此為會出現在 [AppSource](https://appsource.microsoft.com/) 中的名稱。 它最多不能超過 50 個字元。

按一下 [儲存] 即可儲存您的進度。 下一步是為您的供應項目新增技術資訊。

## <a name="fill-out-the-technical-info-form"></a>填寫技術資訊表單


技術資訊表單是填寫您 Dynamics 365 for Customer Engagement 解決方案專屬資訊之處。 滑鼠停留於其上即會為您提供詳細資訊。 請參閱下方的範例。

![技術資訊畫面](./media/CRMScreenShot16.png)

### <a name="application-info"></a>應用程式資訊

大部分的發行者會保留這些欄位的預設值 (使用者、編號)，以及依據上述螢幕擷取畫面，出現空白的應用程式設定 URL。

### <a name="crm-package"></a>CRM 套件

![CRM 套件資訊](./media/CRMScreenShot17.png)

以下是這些欄位的說明：

* 套件的檔案名稱：您建立 CRM AppSource 套件的 zip 檔案時，在前述步驟中建立的檔案名稱。 在上述範例中，其為 "Microsoft\_SamplePackage.zip"。
* 套件位置的 URL：此為上方指定的套件檔案名稱所屬 Azure 儲存體帳戶的 URL。 其為上一節步驟 9 時所建立的 URL。
* 您的套件檔案中是否有多個 crm 套件：**只有**在您支援含有不同套件的多個 CRM 版本時，才選取 [是]。 對大部分的合作夥伴來說，應為 [否]。 若選擇了 [是]，則需要為每一個解決方案的版本，建立 AppSource 套件。 _注意：這並不是在詢問您是否有多個 **zip** 檔案。若您有多個 solution.zip 檔案，但只有一個版本，仍應選取 [否]。套件工具會自動將這些整合在一起。_

### <a name="crm-package-availability"></a>CRM 套件可用性

在本節中，請選取可使用您套件的 CRM 區域。 如需所在區域服務哪些國家/地區的詳細資訊，請參閱連結：[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

注意：部署至德國「主權與美國政府雲端」主權區域，需要特殊權限且認證期間需要進行驗證

## <a name="storefront-details"></a>店面詳細資料

### <a name="offer-summary"></a>供應項目摘要

這是供應項目價值主張的摘要。 其會出現在供應項目的搜尋頁面上， 且上限不可超過 100 個字元。

### <a name="offer-description"></a>供應項目描述

此為出現在您應用程式詳細資料頁面上的描述。 上限為 1300 個字元

### <a name="industries"></a>產業

請選取您應用程式最適用的產業。 若您的應用程式適用於多個產業，可將此保留空白。

### <a name="categories"></a>Categories

選取與您應用程式相相關的類別。 最多可選取 3 個。

### <a name="app-type"></a>應用程式類型

選取您應用程式在 AppSource 上提供的試用類型。 [免費] 表示您的應用程式免費。 [試用] 表示客戶能夠在 AppSource 上短期試用您的應用程式。 Dynamics 365 for Customer Engagement 的應用程式不提供 [要求試用]。 請勿選取此選項。

### <a name="help-link-for-your-app"></a>應用程式的說明連結

在有您應用程式說明相關資訊的頁面，輸入 URL。

### <a name="supported-countriesregions"></a>支援的國家/區域

此欄位可決定您供應項目可供試用的國家/區域。

### <a name="supported-languages"></a>支援的語言

選取您應用程式支援的語言。 若您的應用程式支援清單中未列出的其他語言，請繼續發佈您的供應項目，並寄送電子郵件至 [appsource@microsoft.com](mailto:appsource@microsoft.com)，讓我們知道。

### <a name="app-version"></a>應用程式版本

輸入您應用程式的版本號碼

### <a name="app-release-date"></a>應用程式發佈日期

輸入您應用程式的發佈日期

### <a name="products-your-app-works-with-max-3"></a>可搭配您應用程式的產品 (最多 3 個)

列出可搭配您應用程式的特定產品。 最多可列出三個產品。 若要列出產品，請按一下位於 [新增] 旁的加號，隨即會為您建立新的開放文字欄位，供您輸入可搭配您應用程式的產品名稱。

### <a name="search-keywords-max-3"></a>搜尋關鍵字 (最多 3 個)

客戶可利用 AppSource 根據關鍵字搜尋。 您可輸入一組您應用程式會展現給客戶的關鍵字。

例如，若應用程式是「我的電子郵件服務」電子郵件、傳送郵件、郵件服務，都可以是關鍵字。 在 AppSource 搜尋方塊中選擇使用者可能會用來搜尋您應用程式的字詞。

### <a name="hide-key"></a>隱藏金鑰

其為會與供應項目預覽 URL 相結合，而不顯示在公用檢視中的金鑰。 它並非密碼。 您可於此處輸入任何字串。

### <a name="offer-logo-png-format-48x48"></a>供應項目標誌 (PNG 格式，48x48)

它將顯示在供應項目的搜尋頁面上。 **只允許 png 格式。** 上傳 48PX\*48PX 解析度的 png 影像

### <a name="offer-logo-png-format-216x216"></a>供應項目標誌 (PNG 格式，216x216)

其會出現在您應用程式詳細資料的頁面。 **只允許 PNG 格式。** 上傳 216PX\*216PX 解析度的 png 影像

### <a name="videos"></a>影片

最多可上傳四段影片。 對於每個想要上傳的影片，都需要填寫影片名稱、URL (僅限 YouTube 或 Vimeo) 及和影片相關聯的縮圖。 縮圖必須是 PNG 格式，且必須為1280PX\*720PX。 若要新增影片，請按一下加號。 影片縮圖會出現在您應用程式詳細資料的頁面上。

### <a name="documents"></a>文件

最多可上傳三個 PDF 格式的文件。 對於每個想要上傳的文件，都需要填寫文件名稱，然後再上傳文件。 文件必須是 PDF 格式。

若要新增文件，請按一下加號

### <a name="screenshots"></a>螢幕擷取畫面

這些是會出現在您應用程式 AppSource 詳細資料頁面上的螢幕擷取畫面。

### <a name="privacy-policy"></a>隱私權原則

輸入您應用程式隱私權原則的 URL

### <a name="terms-of-use"></a>使用規定

輸入您應用程式的使用條款。 AppSource 客戶必須先接受這些條款，才可試用您的應用程式

### <a name="support-url"></a>支援 URL

輸入您應用程式的支援 URL。

### <a name="lead-destination"></a>潛在客戶目的地

選取將儲存您潛在客戶的 CRM 系統。 如果您具備下列其中一個 CRM 系統，在此請選取 [Azure 資料表]：Salesforce、Marketo、Microsoft Dynamics CRM。 我們會於您於此處所選的 CRM 系統上，寫入在 AppSource (潛在客戶) 上試用您應用程式的終端使用者詳細資料。 請根據您所選取的 CRM 系統，按一下下面相對應的 URL 來取得完成下一組欄位的資訊

* [Azure 資料表](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>店面詳細資料

合約詳細資料僅用於合作夥伴與 Microsoft 之間的內部溝通之用。 注意：在這些欄位中請務必使用受到監視的電子郵件地址。 我們會使用此電子郵件與您連絡發佈至 AppSource 的進度。 客戶只看得見支援 URL。
