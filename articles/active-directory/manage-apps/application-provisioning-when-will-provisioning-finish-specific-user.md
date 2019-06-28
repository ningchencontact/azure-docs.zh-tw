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
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310042"
---
# <a name="check-the-status-of-user-provisioning"></a>核取的使用者佈建狀態

Azure AD 佈建服務會針對來源系統與目標系統，後面接著定期增量的循環執行初始的佈建循環。 當您設定佈建應用程式時，您可以檢查佈建服務的目前狀態，並查看 當使用者將能夠存取應用程式。

## <a name="view-the-provisioning-progress-bar-preview"></a>檢視佈建進度列 （預覽）

 在 **佈建**頁面為應用程式，您可以檢視 Azure AD 佈建服務的狀態。 **目前的狀態**在頁面底部的區段會顯示佈建循環是否已開始佈建使用者帳戶。 您可以觀察循環的進度、 多少使用者和群組已佈建，請參閱以及多少角色會建立。

當您第一次設定自動佈建**目前的狀態**在頁面底部的區段會顯示初始的佈建循環的狀態。 本節中更新每個增量的循環執行的時間。 顯示下列詳細資料：
- 佈建循環 （初始或累加式） 的類型，目前正在執行或最後一次已完成。
- A**進度列**顯示已完成佈建循環的百分比。 百分比會反映佈建的頁面的計數。 請注意，每個頁面可以包含多個使用者或群組，因此百分比並不直接相互關聯的使用者數目、 群組或佈建的角色。
- A**重新整理**可用來保留更新檢視的按鈕。
- 數目**使用者**並**群組**佈建，以及建立的角色數目。 在初始的週期中，**使用者**數目計數 1 和使用者建立或更新，它向下計數 1 已刪除使用者時。 在累加的週期中，不會影響使用者更新**使用者**計數; 建立或刪除使用者時，只有數字的變更。
- A**檢視稽核記錄**連結，用於使用者佈建服務，包括佈建狀態為個別使用者所執行的所有作業的相關詳細資料，這會開啟 Azure AD 稽核記錄檔 (請參閱[使用稽核記錄](#use-audit-logs-to-check-a-users-provisioning-status)下一節)。

佈建循環完成之後，**統計資料的日期**區段會顯示使用者和群組佈建的日期，以及完成日期和最後一個週期持續時間的累計數字。 **活動識別碼**唯一識別的最新的佈建循環。 **的作業識別碼**是佈建作業的唯一識別碼，並且專屬於您的租用戶中的應用程式。

可在中檢視 Azure 入口網站中，佈建的進度**Azure Active Directory&gt;企業應用程式&gt;\[應用程式名稱\]&gt;佈建** 索引標籤。

![佈建頁面進度列](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>使用稽核記錄檔，以檢查使用者的佈建狀態

若要查看所選使用者的佈建狀態，請參閱 Azure AD 中的稽核記錄。 使用者佈建服務所執行的所有作業都會都記錄在 Azure AD 稽核記錄檔。 這包括所有讀取和寫入作業對來源和目標系統，以及上次讀取或寫入每個作業期間的使用者資料。

在 Azure 入口網站中，可存取佈建的稽核記錄**Azure Active Directory&gt;企業應用程式&gt;\[應用程式名稱\]&gt;稽核記錄** 索引標籤。基於 [帳戶佈建]  類別來篩選記錄，只顯示該應用程式的佈建事件。 您可以根據「比對識別碼」來搜尋使用者，此識別碼是在屬性對應中針對使用者所設定。 

例如，如果您設定了「使用者主體名稱」或「電子郵件地址」做為 Azure AD 端的比對屬性，而且未佈建的使用者具有 "audrey@contoso.com" 的值，接著搜尋 “audrey@contoso.com” 的稽核記錄，然後檢閱傳回的項目。

佈建的稽核記錄會記錄佈建服務所執行的所有作業，包括：

* 查詢位於佈建範圍內之已指派使用者的 Azure AD
* 查詢目標應用程式以確定那些使用者是否存在
* 比較系統之間的使用者物件
* 根據比較，在目標系統中新增、更新或停用使用者帳戶

如需有關如何讀取 Azure 入口網站中的稽核記錄的詳細資訊，請參閱 <<c0> [ 佈建報告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>佈建使用者需要多久時間？
當使用自動使用者佈建與應用程式時，Azure AD 自動佈建，並更新等項目為基礎的應用程式中的使用者帳戶[使用者和群組指派](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)在定期排定的時間間隔，通常每隔 10 分鐘。

針對指定的使用者佈建所需時間取決於主要是您佈建作業是否正在執行初始同步處理 」 或 「 增量同步處理。

- 針對**初始同步處理**，作業時間取決於許多因素，包括佈建，範圍中的使用者和群組的數目和執行的使用者和來源系統中的群組總數。 根據 Azure AD 目錄大小和佈建範圍中的使用者數目而定，Azure AD 和應用程式之間的第一次同步處理可能會花費 20 分鐘到數小時。 本節稍後將完整列出會影響初始同步處理效能的因素摘要。

- 針對**增量同步處理**初始同步處理之後，作業時間通常會更快 (例如 10 分鐘內），因為佈建服務會儲存浮水印，代表這兩個系統的狀態，初始同步處理之後，改善效能後續的同步處理。 在工作時間取決於佈建的該週期中偵測到的變更數目。 如果有少於 5000 的使用者或群組成員資格變更，可以完成作業，在單一的累加佈建週期內。 

下表摘要說明常見佈建案例的同步處理時間。 在這些案例中，來源系統是 Azure AD，而目標系統是 SaaS 應用程式。 同步處理時間被衍生自 ServiceNow、 工作場所、 Salesforce 和 G Suite 的 SaaS 應用程式的同步處理作業的統計分析。


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


針對 [僅同步已指派的使用者與群組]  設定，您可以使用下列公式來判斷**初始同步處理**時間的預估最小值和最大值：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影響**初始同步處理**完成時間的因素摘要：

- 佈建範圍中的使用者和群組總數。

- 來源系統 (Azure AD) 中存在的使用者、群組和群組成員總數。

- 無論使用者佈建範圍中的就會對應到目標應用程式中的現有使用者，或必須建立第一次。 為其第一次建立所有使用者的同步處理作業需要約*兩次只要*做為同步處理的作業的所有使用者都會都對應到現有的使用者。

- [稽核記錄](check-status-user-account-provisioning.md)中的錯誤數目。 如果有許多錯誤且佈建服務已進入隔離狀態，效能就會變差。    

- 目標系統實作的要求速率限制和節流設定。 有些目標系統會實作要求率限制和節流，這可能會影響在大型的同步處理作業期間的效能。 在這樣的情況下，太快收到太多要求的應用程式，可能會因此降低其回應速率或關閉連線。 若要改善效能，連接器必須進行調整，傳送應用程式要求的速度不可比應用程式處理這些要求的速度快。 由 Microsoft 所建置的佈建連接器會進行這項調整。 

- 指派群組的數目和大小。 同步指派群組所花的時間可能比同步使用者的時間長。 指派群組的數目和大小會影響效能。 如果應用程式[啟用群組物件同步處理的對應](customize-application-attributes.md#editing-group-attribute-mappings)，則除了使用者外，群組屬性 (例如群組名稱和成員資格) 也會一起同步。 比起只同步使用者物件，這些額外的同步處理將會花費更長時間。

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者佈建和解除佈建](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
