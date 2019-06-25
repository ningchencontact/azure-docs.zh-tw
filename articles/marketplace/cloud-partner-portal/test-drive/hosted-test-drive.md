---
title: 裝載試用產品 |Azure Marketplace
description: 如何設定及維護 Marketplace 裝載的試用產品
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b8f9ca96ac9386037460ad5c1c9f56fe7b9c2e18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939988"
---
# <a name="hosted-test-drive"></a>裝載的試用產品

「裝載的試用產品」透過由 Microsoft 提供服務裝載環境免除了設定的複雜性，而且它會維護執行試用產品使用者佈建及取消佈建的服務。 此文章的目標對象是在 AppSource 上提供供應項目或發行新供應項目並想要提供「裝載的試用產品」的發行者，「裝載的試用產品」會與 Dynamics 365 for Customer Engagement、Dynamics 365 for Finance 與 Operations 或 Dynamics 365 Business Central 執行個體連結。

## <a name="how-to-publish-a-test-drive"></a>如何發行試用產品

瀏覽到現有供應項目或建立新供應項目。

從側邊功能表選取 [試用產品] 選項。

針對 [啟用試用產品]\'\' 選項選擇 [是]\'\'。

在 \'[詳細資料]\' 區段中提供下列欄位。

- **描述**：提供您的試用產品的概觀。 佈建試用產品時，此文字會顯示給使用者。 此欄位支援 HTML，因此您可以提供格式豐富的內容。
- **使用者手冊**:上傳詳細的使用者手動 （的型別.pdf 檔案），協助了解如何使用您的應用程式的測試使用者。
- **試用產品示範影片**:（選擇性） 上傳影片，能展現您的應用程式。

使用[這裡](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)的指示， 將在您的租用戶中佈建及取消佈建試用產品使用者的權限授與 AppSource。

在此步驟中，您將會產生 \'Azure AD App 識別碼\'與 \'Azure AD App 金鑰\'值，如下所示。

在 [技術設定]\'\' 區段中提供下列欄位：

- **測試磁碟機類型**:選擇\'Microsoft 託管 （例如 Dynamics 365 for Customer Engagement） ' 選項。 這表示 Microsoft 將會裝載並維護執行試用產品使用者佈建及取消佈建的服務。
- **最大並行測試磁碟機**:此欄位設為在指定的任何時間點都有作用中的測試磁碟機的並行使用者數目。 當使用者的試用產品為作用中時，每個使用者都會取用 Dynamics 授權，因此您必須確定您至少有這麼多的 Dynamics 授權可供試用產品使用者使用。 建議值為 3-5 個。
- **測試磁碟機的持續時間 （小時）** :將此欄位設數測試磁碟機將會有作用中的使用者。 在經過設定的小時之後，使用者將從您的租用戶取消佈建。 建議值為 2-24 小時，視您應用程式的複雜度而定。 若使用者用完其時間而且想要再次存取試用產品，一律可以要求進行另一次試用產品。
- **執行個體 URL**:提供試用產品使用者一開始會巡覽至啟動試用產品時的 URL。 這通常是您的應用程式與範例資料安裝所在之 Dynamics 365 執行個體的 URL。 值範例： https:\//testdrive.crm.dynamics.com
- **Azure AD 租用戶識別碼**:提供您的 Dynamics 365 執行個體的 Azure 租用戶的識別碼。 若要擷取此值，請登入 Azure 入口網站並瀏覽到 \'Azure Active Directory\' -\> 從功能表刀鋒視窗選取 [屬性]-\> 複製目錄識別碼。 範例值：72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD 應用程式識別碼**:您在步驟 7 中建立之 Azure AD 應用程式的識別碼。 \ 範例值：53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD 應用程式金鑰**:在建立的 Azure AD 應用程式祕密步驟 7。 \ 範例值：IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Azure AD 租用戶名稱**:提供您的 Dynamics 365 執行個體的 Azure 租用戶的名稱。 使用 \<tenantname.\>onmicrosoft.com 格式。 範例值：testdrive.onmicrosoft.com
- **執行個體的 Web API URL**:提供您的 Dynamics 365 執行個體的 Web API URL。 您可以透過登入您的 Microsoft Dynamics 365 執行個體並瀏覽到 [設定 -\> 自訂 -\> 開發人員資源 -\> 執行個體 Web API (複製此 URL)] 以擷取此值。 範例值： https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **角色名稱**:提供您建立試用產品的自訂 Dynamics 365 安全性角色的名稱。 這是在使用者的試用產品期間將指派給使用者的角色。 範例值： testdriverole

## <a name="next-steps"></a>後續步驟

一切就緒後 **發佈** 供應項目，您的應用程式已通過認證之後, 您將擁有 **預覽** 供應項目。 在 UI 中啟動試用產品，並確認您的試用產品正確執行。 一旦您認為與您預覽供應項目，現在就可以開始 **上線 ！**
