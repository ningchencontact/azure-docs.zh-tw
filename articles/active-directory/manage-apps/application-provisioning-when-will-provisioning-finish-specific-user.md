---
title: 查明特定使用者何時將能存取應用程式 | Microsoft Docs
description: 如何查明非常重要的使用者何時能夠存取您已設定的應用程式，以透過 Azure AD 進行使用者佈建
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c6ad7e305958131c4f544dfa2022e7471e9adac
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147113"
---
# <a name="check-the-status-of-user-provisioning"></a>檢查使用者布建的狀態

Azure AD 布建服務會對來源系統和目標系統執行初始布建週期，後面接著週期性增量迴圈。 當您設定應用程式的布建時，您可以檢查布建服務的目前狀態，並查看使用者何時將能夠存取應用程式。

## <a name="view-the-provisioning-progress-bar"></a>查看布建進度列

 在應用程式的 [布建] 頁面上，您可以查看 Azure AD 布建**服務的狀態**。 頁面底部的 [**目前狀態**] 區段會顯示布建週期是否已開始布建使用者帳戶。 您可以觀賞迴圈的進度、查看已布建多少個使用者和群組，以及查看有多少角色已建立。

當您第一次設定自動布建時，頁面底部的 [**目前狀態**] 區段會顯示初始布建週期的狀態。 此區段會在每次執行增量迴圈時進行更新。 會顯示下列詳細資料：
- 目前正在執行或上次完成的布建週期類型（初始或增量）。
- 顯示已完成布建週期百分比的**進度**列。 百分比會反映已布建的頁面計數。 請注意，每個頁面都可以包含多個使用者或群組，因此百分比不會直接與所布建的使用者、群組或角色數目相互關聯。
- 您可以使用 [重新整理 **] 按鈕來**讓視圖保持更新。
- 已布建的**使用者**和**群組**數目，以及已建立的角色數目。 在初始迴圈期間，使用者**數目會在建立**或更新使用者時計算1，並在使用者被刪除時計算為1。 在增量迴圈期間，使用者更新不會影響**使用者**計數;只有在建立或刪除使用者時，數位才會變更。
- **View Audit logs**連結，會開啟 Azure AD 的 audit 記錄檔，以取得使用者布建服務所執行之所有作業的詳細資料，包括個別使用者的布建狀態（請參閱下面的「[使用 Audit 記錄](#use-audit-logs-to-check-a-users-provisioning-status)」一節）。

布建週期完成之後，[**統計資料]** 區段會顯示已布建到 [日期] 的使用者和群組累計數目，以及最後一個週期的完成日期和持續時間。 **活動識別碼**會唯一識別最新的布建週期。 **工作**識別碼是布建工作的唯一識別碼，而且是您租使用者中的應用程式專用。

布建進度可以在 Azure 入口網站的 [ **Azure Active Directory &gt; Enterprise Apps\] &gt; \[應用程式名稱&gt;**  ] [布建] 索引標籤中查看。

![布建頁面進度列](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>使用 audit 記錄來檢查使用者的布建狀態

若要查看所選使用者的佈建狀態，請參閱 Azure AD 中的稽核記錄。 使用者布建服務所執行的所有作業都會記錄在 Azure AD audit 記錄檔中。 這包括對來源和目標系統進行的所有讀取和寫入作業，以及在每個操作期間讀取或寫入的使用者資料。

布建的 audit 記錄可以在 Azure 入口網站的 [  **&gt; Azure Active Directory Enterprise Apps &gt; \[應用程式名稱\] &gt; ] [審核記錄**] 索引標籤中存取。基於 [帳戶佈建] 類別來篩選記錄，只顯示該應用程式的佈建事件。 您可以根據「比對識別碼」來搜尋使用者，此識別碼是在屬性對應中針對使用者所設定。 

例如，如果您設定了「使用者主體名稱」或「電子郵件地址」做為 Azure AD 端的比對屬性，而且未佈建的使用者具有 "audrey@contoso.com" 的值，接著搜尋 “audrey@contoso.com” 的稽核記錄，然後檢閱傳回的項目。

佈建的稽核記錄會記錄佈建服務所執行的所有作業，包括：

* 查詢位於佈建範圍內之已指派使用者的 Azure AD
* 查詢目標應用程式以確定那些使用者是否存在
* 比較系統之間的使用者物件
* 根據比較，在目標系統中新增、更新或停用使用者帳戶

如需有關如何在 Azure 入口網站中讀取 audit 記錄的詳細資訊，請參閱布建[報告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>佈建使用者需要多久時間？
在應用程式中使用自動使用者布建時，Azure AD 會根據定期排程的時間[間隔（通常](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)是每40分鐘），在應用程式中自動布建和更新使用者帳戶。

提供給指定使用者所需的時間，主要取決於您的布建作業是執行初始同步處理還是增量同步處理。

- 進行**初始同步**處理時，作業時間取決於許多因素，包括布建範圍中的使用者和群組數目，以及來源系統中的使用者和群組總數。 根據 Azure AD 目錄大小和佈建範圍中的使用者數目而定，Azure AD 和應用程式之間的第一次同步處理可能會花費 20 分鐘到數小時。 本節稍後將完整列出會影響初始同步處理效能的因素摘要。

- 針對初始同步處理之後的**增量同步**處理，工作時間通常會更快（例如在10分鐘內），因為布建服務會在初始同步處理後儲存代表兩個系統狀態的浮水印，以改善後續同步處理的效能。 作業時間取決於在該布建週期中偵測到的變更數目。 如果使用者或群組成員資格變更少於5000個，則工作可以在單一累加布建週期內完成。 

下表摘要說明常見佈建案例的同步處理時間。 在這些案例中，來源系統是 Azure AD，而目標系統是 SaaS 應用程式。 同步處理時間衍生自 SaaS 應用程式 ServiceNow、Workplace、Salesforce 和 G Suite 的同步作業統計分析。


| 範圍設定 | 範圍中的使用者、群組和成員 | 初始同步處理時間 | 增量同步處理時間 |
| -------- | -------- | -------- | -------- |
| 僅同步已指派的使用者與群組 |  < 1,000 |  < 30 分鐘 | < 30 分鐘 |
| 僅同步已指派的使用者與群組 |  1,000 - 10,000 | 142 - 708 分鐘 | < 30 分鐘 |
| 僅同步已指派的使用者與群組 |   10,000 - 100,000 | 1,170 - 2,340 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  < 1,000 | < 30 分鐘  | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  1,000 - 10,000 | < 30 - 120 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  10,000 - 100,000  | 713 - 1,425 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者|  < 1,000  | < 30 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者 | 1,000 - 10,000  | 43 - 86 分鐘 | < 30 分鐘 |


針對 [僅同步已指派的使用者與群組] 設定，您可以使用下列公式來判斷**初始同步處理**時間的預估最小值和最大值：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影響**初始同步處理**完成時間的因素摘要：

- 佈建範圍中的使用者和群組總數。

- 來源系統 (Azure AD) 中存在的使用者、群組和群組成員總數。

- 布建範圍中的使用者是否符合目標應用程式中的現有使用者，或必須第一次建立。 所有使用者第一次建立的同步作業，只要將所有使用者都符合現有使用者的同步作業，就會花上*兩倍*的時間。

- [稽核記錄](check-status-user-account-provisioning.md)中的錯誤數目。 如果有許多錯誤且佈建服務已進入隔離狀態，效能就會變差。    

- 目標系統實作的要求速率限制和節流設定。 某些目標系統會執行要求速率限制和節流，這可能會影響大型同步處理作業期間的效能。 在這樣的情況下，太快收到太多要求的應用程式，可能會因此降低其回應速率或關閉連線。 若要改善效能，連接器必須進行調整，傳送應用程式要求的速度不可比應用程式處理這些要求的速度快。 由 Microsoft 所建置的佈建連接器會進行這項調整。 

- 指派群組的數目和大小。 同步指派群組所花的時間可能比同步使用者的時間長。 指派群組的數目和大小會影響效能。 如果應用程式[啟用群組物件同步處理的對應](customize-application-attributes.md#editing-group-attribute-mappings)，則除了使用者外，群組屬性 (例如群組名稱和成員資格) 也會一起同步。 比起只同步使用者物件，這些額外的同步處理將會花費更長時間。

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者佈建和解除佈建](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
