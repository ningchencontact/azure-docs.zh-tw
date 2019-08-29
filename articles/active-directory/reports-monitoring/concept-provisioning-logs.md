---
title: 在 Azure Active Directory 入口網站中布建記錄 (預覽) |Microsoft Docs
description: 在 Azure Active Directory 入口網站中布建活動報告的簡介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b2d80d21822758991ad8b3bc1d3bb44e2257f4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129805"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>在 Azure Active Directory 入口網站中布建報告 (預覽)

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - [稽核記錄](concept-audit-logs.md)可提供使用者和群組管理、受控應用程式和目錄活動的相關系統活動資訊。
    - 布建**記錄**-提供有關 Azure AD 布建服務所布建之使用者、群組和角色的系統活動。 

- **安全性** 
    - **有風險的登入** - [有風險的登入](concept-risky-sign-ins.md)表示非使用者帳戶合法擁有者的某人嘗試登入。
    - **標幟為有風險的使用者** - [有風險的使用者](concept-user-at-risk.md)表示可能被盜用的使用者帳戶。

本主題提供布建報告的總覽。

## <a name="prerequisites"></a>必要條件

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 安全性系統管理員、安全性讀取者、報告讀取者、應用程式系統管理員和雲端應用程式系統管理員角色中的使用者
* 全域系統管理員


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>您需要哪些 Azure AD 授權才能存取布建活動？

您的租使用者必須有相關聯的 Azure AD Premium 授權, 才能查看所有布建活動報告。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 

## <a name="provisioning-logs"></a>布建記錄

布建記錄提供下列問題的解答:

* 已成功在 ServiceNow 中建立哪些群組？
* 如何從 Amazon Web Services 匯入角色？
* DropBox 中未成功建立的使用者為何？

您可以在[Azure 入口網站](https://portal.azure.com)的 [ **Azure Active Directory** ] 分頁的 [**監視**] 區段中, 選取 [布建**記錄**] 來存取布建記錄。 可能需要最多兩個小時的時間, 才能在入口網站中顯示某些布建記錄。

布建![記錄]布建(./media/concept-provisioning-logs/access-provisioning-logs.png "記錄")


布建記錄檔具有預設清單視圖, 顯示:

- 身分識別
- 動作
- 來源系統
- 目標系統
- 狀態
- 日期


![預設資料行](./media/concept-provisioning-logs/default-columns.png "預設資料行")

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

資料![行選擇器]資料(./media/concept-provisioning-logs/column-chooser.png "行選擇器")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![可用]的資料行(./media/concept-provisioning-logs/available-columns.png "可用")的資料行

選取清單檢視中的項目，即可取得更詳細的資訊。

![詳細資訊](./media/concept-provisioning-logs/detailed-information.png "詳細資訊")


## <a name="filter-provisioning-activities"></a>篩選布建活動

若要將報告的資料縮小至適合您的層級, 您可以使用下列預設欄位篩選布建資料。 請注意, 篩選中的值會根據您的租使用者動態填入。 例如, 如果您的租使用者中沒有任何建立事件, 就不會有 [建立] 的篩選選項。

- 身分識別
- Action
- 來源系統
- 目標系統
- 狀態
- Date


![篩選](./media/concept-provisioning-logs/filter.png "篩選")

[身分**識別**] 篩選準則可讓您指定您關心的名稱或身分識別。 此身分識別可以是使用者、群組、角色或其他物件。 您可以依物件的名稱或識別碼進行搜尋。 識別碼會因案例而異。 例如, 從 Azure AD 將物件布建到 SalesForce 時, 來源識別碼是 Azure AD 中使用者的物件識別碼, 而 TargetID 是 Salesforce 中使用者的識別碼。 從 Workday 布建到 Active Directory 時, 來源識別碼是 Workday 背景工作員工識別碼。 請注意, 使用者的名稱不一定會出現在 [標識] 資料行中。 一律會有一個識別碼。 

[**來源系統**] 篩選準則可讓您指定要從何處布建身分識別。 例如, 從 Azure AD 將物件布建到 ServiceNow 時, 會 Azure AD 來源系統。 

[**目標系統**] 篩選準則可讓您指定要將身分識別布建至何處。 例如, 從 Azure AD 將物件布建到 ServiceNow 時, 目標系統是 ServiceNow。 

[狀態] 篩選條件可讓您選取︰

- 全部
- 成功
- 失敗
- 已略過

**動作**篩選準則可讓您篩選:

- 建立 
- Update
- DELETE
- 停用
- 其他

[日期] 篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 30 天
- 24 小時
- 自訂時間間隔

當您選取自訂時間範圍時, 可以設定 [開始日期] 和 [結束日期]。


除了預設欄位, 選取此選項時, 您也可以在篩選中包含下欄欄位:

- **作業識別碼**-唯一的作業識別碼會與您已啟用布建的每個應用程式相關聯。   

- **週期識別碼**-唯一識別布建週期。 您可以共用此識別碼以支援查詢發生此事件的週期。

- **變更 ID** -布建事件的唯一識別碼。 您可以共用此識別碼以支援查詢布建事件。   



  

## <a name="provisioning-details"></a>布建詳細資料 

當您在 [布建清單] 視圖中選取專案時, 您會取得此專案的更多詳細資料。
詳細資料會根據下列分類進行分組:

- 步驟

- 疑難排解和建議

- 修改過的屬性

- 總結


![篩選](./media/concept-provisioning-logs/provisioning-tabs.png "")索引標籤



### <a name="steps"></a>步驟

[**步驟**] 索引標籤會列出布建物件所採取的步驟。 布建物件可包含四個步驟: 

- 匯入物件
- 判斷物件是否在範圍內
- 來源與目標之間的相符物件
- 布建物件 (採取動作-這可能是 [建立]、[更新]、[刪除] 或 [停用])



![篩選](./media/concept-provisioning-logs/steps.png "篩選")


### <a name="troubleshoot-and-recommendations"></a>疑難排解和建議


[**疑難排解和建議**] 索引標籤會提供錯誤碼和原因。 錯誤資訊僅適用于失敗的情況。 


### <a name="modified-properties"></a>修改過的屬性

**修改後的屬性**會顯示舊值和新值。 在沒有舊值的情況下, [舊值] 資料行是空白的。 


### <a name="summary"></a>總結

[**摘要**] 索引標籤可讓您大致瞭解來源和目標系統中物件的發生狀況和識別碼。 

## <a name="what-you-should-know"></a>您應該知道的事情

- 如果您有 premium edition 和7天的免費版本, Azure 入口網站會將報告的布建資料儲存30天。

- 您可以使用變更 ID 屬性作為唯一識別碼。 例如, 當與產品支援互動時, 這會很有説明。

- 目前沒有任何選項可下載布建資料。

- 目前不支援 log analytics。

- 當您從應用程式的內容存取布建記錄時, 它不會自動將事件篩選至特定應用程式, 而不會有 audit 記錄的執行方式。

## <a name="next-steps"></a>後續步驟

* [檢查使用者布建的狀態](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [設定 Azure AD 資源庫應用程式的使用者布建時發生問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


