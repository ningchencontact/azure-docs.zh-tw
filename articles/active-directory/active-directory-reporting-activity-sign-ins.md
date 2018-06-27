---
title: Azure Active Directory 入口網站中的登入活動報告 | Microsoft Docs
description: 介紹 Azure Active Directory 入口網站中的登入活動報告
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/17/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d9c2f210204122947c24eb42f643450537f3b9a8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231896"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的登入活動報告

透過 [Azure 入口網站](https://portal.azure.com)中的 Azure Active Directory (Azure AD) 報告，您可以取得判斷您的環境執行狀況所需的資訊。

Azure Active Directory 中的報告架構包含下列元件：

- **活動** 
    - **登入活動** – 受控應用程式和使用者登入活動的使用情況相關資訊
    - **稽核記錄** - 關於使用者和群組管理、受控應用程式和目錄活動的系統活動資訊。
- **Security** 
    - **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 如需詳細資訊，請參閱＜有風險的登入＞。
    - **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 如需詳細資訊，請參閱＜標幟為有風險的使用者＞。

本主題提供登入活動的概觀。

## <a name="prerequisites"></a>先決條件

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 具有安全性系統管理員、安全性讀取者或報告讀取者角色的使用者
* 全域管理員
* 任何使用者 (非系統管理員) 都可以存取自己的登入 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>您需要哪項 Azure AD 授權才能存取登入活動？
* 租用戶必須要有相關聯的 Azure AD Premium 授權，才能查看活動報告中的所有登入


## <a name="sign-in-activities"></a>登入活動

利用使用者登入報告所提供的資訊，您可以找到下列問題的解答︰

* 使用者的登入模式為何？
* 一週內有多少使用者登入？
* 這些登入的狀態為何？

所有登入活動資料的第一個進入點是 **Azure Active** [活動] 區段中的 [登入]。


![登入活動](./media/active-directory-reporting-activity-sign-ins/61.png "登入活動")


登入記錄的預設清單檢視會顯示︰

- 登入日期
- 相關的使用者
- 使用者已登入的應用程式
- 登入狀態
- 風險偵測的狀態
- 多重要素驗證 (MFA) 需求的狀態 

![登入活動](./media/active-directory-reporting-activity-sign-ins/01.png "登入活動")

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![登入活動](./media/active-directory-reporting-activity-sign-ins/19.png "登入活動")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![登入活動](./media/active-directory-reporting-activity-sign-ins/02.png "登入活動")

按一下清單檢視中的項目，即可取得所有可用的詳細資料 (水平檢視)。

![登入活動](./media/active-directory-reporting-activity-sign-ins/03.png "登入活動")


## <a name="filter-sign-in-activities"></a>篩選登入活動

若要將報告的資料縮小至您適用的層級，您可以使用下列預設欄位篩選登入資料︰

- 使用者
- Application
- 登入狀態
- 風險偵測的狀態
- 日期


![登入活動](./media/active-directory-reporting-activity-sign-ins/04.png "登入活動")

[使用者] 篩選條件可讓您指定您關心的使用者名稱或使用者主體名稱 (UPN)。 

[應用程式] 篩選條件可讓您指定您關心的應用程式名稱。  

[登入狀態] 篩選條件可讓您選取︰

- 全部 
- 成功
- 失敗

[偵測到的風險] 篩選條件可讓您選取︰

- 全部
- yes
- 否 


[日期] 篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 24 小時
- 自訂時間間隔

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

如果您將其他欄位新增至您的登入檢視，這些欄位就會自動新增至篩選條件清單。 例如，藉由將 [用戶端應用程式] 欄位新增至您的清單，同時也會取得其他篩選選項，可讓您設定下列篩選條件：

- [瀏覽器]      
- Exchange ActiveSync (支援)               
- Exchange ActiveSync (不支援)
- 其他用戶端               
    - IMAP
    - MAPI
    - 舊版 Office 用戶端
    - POP
    - SMTP


![登入活動](./media/active-directory-reporting-activity-sign-ins/12.png "登入活動")


> [!TIP] 
> 除了預設篩選條件以外，您新增至登入檢視的所有其他欄位，都會成為篩選欄位。


## <a name="download-sign-in-activities"></a>下載登入活動

如果您想要在 Azure 入口網站以外使用登入活動資料，您可以下載該資料。 除了 [下載] 按鈕以外，Azure 入口網站也提供讓您產生指令碼以下載資料的選項。  

![下載](./media/active-directory-reporting-activity-sign-ins/71.png "下載")

按一下 [下載]，以建立最近 5000 筆記錄的 CSV 檔案。 如果您需要更多彈性，您可以使用指令碼解決方案。 按一下 [指令碼]，這樣會建立一個 PowerShell 指令碼，而且它包含所有您已設定的篩選條件。 下載此指令碼並在**系統管理員模式**中執行來產生 CSV 檔案。 除了技術實作以外，您可以下載的記錄數目也會受限於 [Azure Active Directory 報告保留原則](active-directory-reporting-retention.md)。  



## <a name="sign-in-activities-shortcuts"></a>登入活動捷徑

除了 Azure Active Directory 之外，Azure 入口網站可提供您登入活動資料的兩個額外進入點︰

- 身分識別安全性保護概觀
- 使用者
- 群組
- 企業應用程式


### <a name="users-sign-ins-activities"></a>使用者登入活動

利用使用者登入報告所提供的資訊，您可以找到下列問題的解答︰

- 使用者的登入模式為何？
- 一週內有多少使用者登入？
- 這些登入的狀態為何？



此資料的進入點是 [身分識別安全性保護] 概觀頁面中的使用者登入圖。 使用者登入圖會顯示在指定的時間週期中所有使用者的每週登入彙總。 時間週期的預設值是 30 天。

![登入活動](./media/active-directory-reporting-activity-sign-ins/06.png "登入活動")

當您按一下登入圖中的某一天時，會取得當日登入活動的概觀。


登入活動清單中的每一列會顯示：

* 誰已登入？
* 哪個應用程式是登入的目標？
* 登入狀態為何？
* 登入的 MFA 狀態為何？

按一下項目，即可取得有關登入作業的更多詳細資料：

- 使用者識別碼
- 使用者
- 使用者名稱
- 應用程式識別碼
- Application
- 用戶端
- 位置
- IP 位址
- 日期
- 需要 MFA
- 登入狀態

 
在 [使用者] 頁面上，按一下 [活動] 區段中的 [登入]，即可取得所有使用者登入的完整概觀。

![登入活動](./media/active-directory-reporting-activity-sign-ins/08.png "登入活動")




## <a name="usage-of-managed-applications"></a>受控應用程式的使用情況

利用登入資料以應用程式為主的檢視，您可以回答下列問題︰

* 誰在使用我的應用程式？
* 您的組織中排名前 3 個應用程式為何？
* 我最近已推出一個應用程式。 它的情況為何？

此資料的進入點是 [企業應用程式] 頁面的 [概觀] 區段中「最近 30 天內您的組織中排名前 3 個應用程式」報告。

![登入活動](./media/active-directory-reporting-activity-sign-ins/10.png "登入活動")

應用程式使用圖會顯示在指定的時間週期中排名前 3 個應用程式的每週登入彙總。 時間週期的預設值是 30 天。

![登入活動](./media/active-directory-reporting-activity-sign-ins/47.png "登入活動")

如果您想要，您可以將焦點設在特定的應用程式。


![報告](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "報告")

當您按一下應用程式使用圖中的某一天時，您會取得登入活動的詳細清單。




[登入]  選項會提供您的應用程式的所有登入事件的完整概觀。

![登入活動](./media/active-directory-reporting-activity-sign-ins/11.png "登入活動")



## <a name="next-steps"></a>後續步驟

如果您想深入了解登入活動錯誤碼，請參閱 [Azure Active Directory 入口網站中的登入活動報告錯誤碼](active-directory-reporting-activity-sign-ins-errors.md)。

