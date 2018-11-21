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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ae962cba5e3d08661eb1c93edfc2feb221a9367e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623759"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的登入活動報告

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - [稽核記錄](concept-audit-logs.md)可提供使用者和群組管理、受控應用程式和目錄活動的相關系統活動資訊。
- **安全性** 
    - **有風險的登入** - [有風險的登入](concept-risky-sign-ins.md)表示非使用者帳戶合法擁有者的某人嘗試登入。
    - **標幟為有風險的使用者** - [有風險的使用者](concept-user-at-risk.md)表示可能被盜用的使用者帳戶。

本主題提供登入報告的概觀。

## <a name="prerequisites"></a>必要條件

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 具有安全性系統管理員、安全性讀取者和報告讀取者角色的使用者
* 全域系統管理員
* 此外，任何使用者 (非系統管理員) 都可以存取自己的登入 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>您需要哪項 Azure AD 授權才能存取登入活動？
* 租用戶必須要有相關聯的 Azure AD Premium 授權，才能查看活動報告中的所有登入

## <a name="sign-ins-report"></a>登入報告

使用者登入報告可回答下列問題：

* 使用者的登入模式為何？
* 一週內有多少使用者登入？
* 這些登入的狀態為何？

您可以藉由在 [Azure入口網站](https://portal.azure.com)的 [Azure Active Directory] 刀鋒視窗中，選取 [活動] 區段的 [登入]，來存取登入報告。

![登入活動](./media/concept-sign-ins/61.png "登入活動")

登入記錄的預設清單檢視會顯示︰

- 登入日期
- 相關的使用者
- 使用者已登入的應用程式
- 登入狀態
- 風險偵測的狀態
- 多重要素驗證 (MFA) 需求的狀態

![登入活動](./media/concept-sign-ins/01.png "登入活動")

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![登入活動](./media/concept-sign-ins/19.png "登入活動")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![登入活動](./media/concept-sign-ins/02.png "登入活動")

選取清單檢視中的項目，即可取得更詳細的資訊。

![登入活動](./media/concept-sign-ins/03.png "登入活動")

> [!NOTE]
> 客戶現在可以透過所有的登入報告來針對條件式存取原則進行疑難排解。 按一下登入記錄的 [條件式存取]，客戶可以檢閱條件式存取狀態，並探索套用至每個原則登入和結果的原則詳細資料。
> 如需詳細資訊，請參閱[所有登入中的 CA 資訊相關常見問題集](reports-faq.md#conditional-access)。

![登入活動](./media/concept-sign-ins/ConditionalAccess.png "登入活動")


## <a name="filter-sign-in-activities"></a>篩選登入活動

若要將報告的資料縮小至您適用的層級，您可以使用下列預設欄位篩選登入資料︰

- 使用者
- Application
- 登入狀態
- 條件式存取
- 日期

![登入活動](./media/concept-sign-ins/04.png "登入活動")

[使用者] 篩選條件可讓您指定您關心的使用者名稱或使用者主體名稱 (UPN)。

[應用程式] 篩選條件可讓您指定您關心的應用程式名稱。

[登入狀態] 篩選條件可讓您選取︰

- 全部
- 成功
- 失敗

**條件式存取**篩選條件可讓您選取登入的 CA 原則狀態：

- 全部
- 未套用
- 成功
- 失敗

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


![登入活動](./media/concept-sign-ins/12.png "登入活動")


## <a name="download-sign-in-activities"></a>下載登入活動

如果您想要在 Azure 入口網站以外使用登入活動資料，您可以[下載登入資料](quickstart-download-sign-in-report.md)。 按一下 [下載]，以建立最近 5000 筆記錄的 CSV 檔案。  除了 [下載] 按鈕以外，Azure 入口網站也提供讓您[產生指令碼以下載資料](tutorial-signin-logs-download-script.md)的選項。  

![下載](./media/concept-sign-ins/71.png "下載")

如果您需要更多彈性，您可以使用指令碼解決方案。 按一下 [指令碼]，這樣會建立一個 PowerShell 指令碼，而且它包含所有您已設定的篩選條件。 下載此指令碼並在**系統管理員模式**中執行來產生 CSV 檔案。 

> [!IMPORTANT]
> 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。  

### <a name="running-the-script-on-a-windows-10-machine"></a>在 Windows 10 電腦上執行指令碼

如果您想要在 **Windows 10** 電腦上執行指令碼，必須先執行一些額外的步驟。 

1. 安裝 [AzureRM 模組](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l)。
2. 開啟 PowerShell 提示字元並執行 **Import-module AzureRM** 命令來匯入模組。
3. 執行 **Set-ExecutionPolicy unrestricted** 然後選擇 [全部皆是]。 
4. 現在您可以在系統管理員模式中執行下載的 PowerShell 指令碼執行來產生 CSV 檔案。

## <a name="sign-ins-data-shortcuts"></a>登入資料捷徑

除了 Azure AD 之外，Azure 入口網站還可提供您登入資料的額外進入點︰

- 身分識別安全性保護概觀
- 使用者
- 群組
- 企業應用程式

### <a name="users-sign-ins-data-in-identity-security-protection"></a>身分識別安全性保護中的使用者登入資料

[身分識別安全性保護] 概觀頁面中的使用者登入圖，會顯示在指定的時間週期中所有使用者的每週登入彙總。 時間週期的預設值是 30 天。

![登入活動](./media/concept-sign-ins/06.png "登入活動")

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

![登入活動](./media/concept-sign-ins/08.png "登入活動")

## <a name="usage-of-managed-applications"></a>受控應用程式的使用情況

利用登入資料以應用程式為主的檢視，您可以回答下列問題︰

* 誰在使用我的應用程式？
* 您的組織中排名前 3 個應用程式為何？
* 我最近已推出一個應用程式。 它的情況為何？

此資料的進入點是在 [企業應用程式] 之下 [概觀] 區段中的最近 30 天報告內您的組織中排名前 3 個應用程式。

![登入活動](./media/concept-sign-ins/10.png "登入活動")

應用程式使用圖會顯示在指定的時間週期中排名前 3 個應用程式的每週登入彙總。 時間週期的預設值是 30 天。

![登入活動](./media/concept-sign-ins/47.png "登入活動")

如果您想要，您可以將焦點設在特定的應用程式。

![報告](./media/concept-sign-ins/single_spp_usage_graph.png "報告")

當您按一下應用程式使用圖中的某一天時，您會取得登入活動的詳細清單。

[登入]  選項會提供您的應用程式的所有登入事件的完整概觀。

![登入活動](./media/concept-sign-ins/11.png "登入活動")

## <a name="next-steps"></a>後續步驟

* [登入活動報告錯誤碼](reference-sign-ins-error-codes.md)
* [Azure AD 資料保留原則](reference-reports-data-retention.md)
* [Azure AD 報告延遲](reference-reports-latencies.md)

