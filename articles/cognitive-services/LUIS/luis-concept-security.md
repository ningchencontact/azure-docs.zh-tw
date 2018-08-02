---
title: 了解對 LUIS 應用程式的存取權 - Azure | Microsoft Docs
description: 了解如何存取 LUIS 撰寫。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: fe0ee7d575e69c883ad7f980477e86fbd004778e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397238"
---
# <a name="authoring-and-endpoint-user-access"></a>撰寫和端點的使用者存取權
撰寫存取權適用於擁有者和共同作業者。 就私人應用程式而言，端點存取權適用於擁有者和共同作業者。 就公用應用程式而言，端點存取權適用於擁有自己 LUIS 帳戶且擁有公用應用程式識別碼的所有使用者。 

## <a name="access-to-authoring"></a>存取撰寫
從 [LUIS](luis-reference-regions.md#luis-website) 網站或[撰寫 API](https://aka.ms/luis-authoring-apis) 存取應用程式時，會受到應用程式擁有者所控制。 

擁有者和所有共同作業者都具備可撰寫應用程式的存取權。 

|撰寫存取權包括|注意|
|--|--|
|新增或移除端點金鑰||
|匯出版本||
|匯出端點記錄||
|匯入版本||
|將應用程式設定為公用|當應用程式為公用時，任何具有撰寫或端點金鑰的使用者都可查詢該應用程式。|
|修改模型|
|發佈|
|檢閱用於[主動式學習](luis-how-to-review-endoint-utt.md)的端點語句|
|定型|

## <a name="access-to-endpoint"></a>存取端點
存取端點以查詢 LUIS 時，會受到 [Settings] \(設定\) 頁面上應用程式的 [Public] \(公用\) 設定控管。 系統會檢查私人應用程式端點查詢是否有含有剩餘配額叫用次數的授權金鑰。 公用應用程式的端點查詢必須一併提供端點金鑰 (來自進行查詢的任何使用者)，而系統也會檢查此金鑰是否有剩餘的配額叫用次數。 

端點金鑰會在 GET 要求的查詢字串或 POST 要求的標頭中傳遞。

![將應用程式設定為公用](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>私人應用程式端點安全性
私人應用程式的端點僅供下列金鑰使用：

|金鑰和使用者|說明|
|--|--|--|
|擁有者的撰寫金鑰| 最多 1000 次端點叫用|
|共同作業者的撰寫金鑰| 最多 1000 次端點叫用|
|從**[發佈](luis-how-to-publish-app.md)** 頁面新增的端點金鑰|擁有者和共同作業者可以新增端點金鑰|

其他撰寫金鑰或端點金鑰則**無**存取權。

### <a name="public-app-endpoint-access"></a>公用應用程式端點存取權
在應用程式的 [Settings] \(設定\) 頁面上，將應用程式設定為**公用**。 將應用程式設定為公用之後，「任何」有效的 LUIS 撰寫金鑰或 LUIS 端點金鑰都能查詢您的應用程式，只要該金鑰尚未使用整個端點配額即可。

使用者如果不是擁有者或共同作業者，在取得應用程式識別碼的情況下，只能存取公用應用程式。 LUIS 沒有公用「市集」或其他方式可供搜尋公用應用程式。  

## <a name="microsoft-user-accounts"></a>Microsoft 使用者帳戶
作者和共同作業者可以在 [Publish] \(發佈\) 頁面上將金鑰新增至 LUIS。 在 Azure 入口網站中建立 LUIS 金鑰的 Microsoft 使用者帳戶必須是應用程式擁有者，或應用程式共同作業者。 

若要深入了解 Active Directory 使用者帳戶，請參閱 [Azure Active Directory 租用戶使用者](luis-how-to-collaborate.md#azure-active-directory-tenant-user)。 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>保護端點 
您可以在伺服器對伺服器環境中呼叫 LUIS 端點，來控管可以看見 LUIS 端點金鑰的使用者。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 呼叫伺服器端 API 並確認驗證和授權之後，請將呼叫繼續傳遞給 LUIS。 雖然此策略並無法防止攔截式攻擊，但可向您的使用者混淆端點、讓您能夠追蹤存取，以及讓您新增端點回應記錄功能 (例如[Application Insights](https://azure.microsoft.com/services/application-insights/))。  

## <a name="security-compliance"></a>安全性合規性
LUIS 已成功完成 ISO 27001:2013 和 ISO 27018:2014 稽核，稽核報告中沒有任何不符合規範的項目 (結果)。 此外，LUIS 也已取得「CSA STAR 認證」，獲得成熟度能力評鑑最高可能金級獎。 Azure 是唯一獲得此認證的主要公用雲端服務提供者。 如需詳細資料，您可以在[信任中心](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO 頁面上所引用的 Azure 主要[合規性概觀](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) \(英文\) 文件中，找到包含在已更新範圍聲明中的 LUIS。  

## <a name="next-steps"></a>後續步驟

請參閱[最佳做法](luis-concept-best-practices.md)，以了解如何使用意圖和圖示來獲得最佳預測。