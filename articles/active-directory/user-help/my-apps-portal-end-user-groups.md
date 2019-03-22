---
title: 更新您的群組資訊從 My Apps 入口網站-Azure Active Directory |Microsoft Docs
description: 了解如何檢視並更新您群組相關的資訊，包括檢視您所擁有的群組、 建立新的群組，檢視的群組的目前成員，但是加入任何群組還不太的一部分。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5af86ad12eed4901e577149b9f20216d5004663b
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340457"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>更新您的群組資訊從 My Apps 入口網站
您可以使用工作或學校帳戶，透過以 web 為基礎**我的應用程式**入口網站中，檢視和啟動許多組織的雲端式應用程式，以更新您的設定檔和帳戶資訊，以查看您**群組**的詳細資訊，並執行**存取權檢閱**為您的應用程式及群組。 如果您沒有存取權**My Apps**入口網站中，您必須連絡技術服務人員以權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>本內容適用於使用者。 如果您是系統管理員，您可以找到有關如何設定和管理雲端中的應用程式的詳細資訊[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)。

## <a name="view-your-groups-information"></a>檢視群組資訊
如果您的系統管理員提供了檢視的權限**群組** 圖格，您可以：

- **為群組成員。** 檢視詳細資料，或保留任何群組。

- **身為群組擁有者。** 檢視詳細資料、 建立新的群組、 新增或移除成員，或刪除您的群組。

### <a name="to-view-your-groups-information"></a>若要檢視您的群組資訊

1.  登入您的公司或學校帳戶。

2.  開啟網頁瀏覽器並移至 https://myapps.microsoft.com，或使用貴組織所提供的連結。 比方說，您可能會被導向至自訂頁面為您的組織，例如 https://myapps.microsoft.com/contoso.com。

    **應用程式**頁面隨即出現，顯示的所有雲端應用程式，您的組織所擁有且可供您使用。

    ![在 My Apps 入口網站中的 [應用程式] 頁面](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. 選取 **群組**磚，以查看您群組相關的資訊。

    ![同時擁有使用 [群組] 頁面和成員群組](media/my-apps-portal/my-apps-portal-groups-page.png)

4. 根據您的權限，您可以使用**群組**頁面，即可：

    - **檢閱您所擁有的群組。** 從您組織內的任何相關資訊群組的檢視擁有**我擁有的群組**區域。 選取特定的群組名稱可以提供更多詳細的群組，包括群組類型、 成員數目、 聯結原則和作用中的成員清單。

    - **建立新的群組。** 與您建立新的群組，以從擁有者**我擁有的群組**區域。 如需特定步驟，請參閱[建立新的群組](#create-a-new-group)一節。

    - **編輯現有的群組。** 編輯任何您自己的群組詳細資料。 如需特定步驟，請參閱[編輯現有的群組](#edit-an-existing-group)一節。

    - **新增或移除成員。** 新增或移除您所擁有的群組的成員。 如需特定步驟，請參閱[新增或移除成員](#add-or-remove-a-member)一節。

    - **刪除群組。** 刪除任何您所擁有的群組。 如需特定步驟，請參閱[刪除群組](#delete-a-group)一節。

    - **檢閱您的一部分的群組。** 檢視的名稱的任何群組中，您是從成員**我加入的群組**區域。 選取特定的群組名稱可以提供更多詳細的群組，包括群組類型、 成員數目、 聯結原則和作用中的成員清單。

    - **加入群組。** 加入現有的群組，讓原本不在成員，從**我加入的群組**區域。 如需特定步驟，請參閱[加入現有的群組](#join-an-existing-group)。

## <a name="create-a-new-group"></a>建立新群組
1. 在 **群組**頁面上，選取**建立群組**從**我擁有的群組**區域。

    **建立群組**方塊隨即出現。

    ![建立群組方塊](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. 輸入必要資訊：

    - **群組類型：**
        
        - **安全性。** 用來管理成員和電腦對使用者群組所共用之資源的存取權。 例如，您可以針對特定安全性原則建立安全性群組。 透過這麼做，您可以將一組權限同時授與所有成員，而不必個別為每個成員新增權限。

        - **Office 365.** 透過將共用信箱、行事曆、檔案、SharePoint 網站等的存取權授與成員，來提供共同作業的機會。 此選項也可讓您將群組的存取權授與組織外的人員。

    - **群組名稱。** 為群組新增名稱，請使用易記且合理的名稱。

    - **群組描述 （選擇性）。** 為群組新增選擇性的描述。

    - **群組原則。** 選擇允許所有人加入群組，或只允許群組擁有者，將成員加入。

3. 選取 [建立] 。

    與您建立新的群組為擁有者，並出現在您**我擁有的群組**清單。 因為您是擁有者，此群組也會出現在**我加入的群組**清單。

## <a name="edit-an-existing-group"></a>編輯現有的群組
建立群組之後，您可以編輯其詳細資料，包括更新任何現有的資訊。

### <a name="to-edit-your-details"></a>若要編輯您的詳細資料
1. 選取您想要從編輯的群組**群組**頁面，然後再選取**編輯詳細資料**上*&lt;group_name&gt;* 頁面。

    **編輯詳細資料**方塊隨即出現，而您可以更新您一開始建立群組時新增的資訊。

2. 讓您的所有變更，然後按**更新**。

## <a name="add-or-remove-a-member"></a>新增或移除成員
您可以新增或移除任何您自己的群組的成員。

### <a name="to-add-or-remove-a-member"></a>若要新增或移除成員
1. 選取您想要將成員加入，然後選取的群組**+** 上*&lt;group_name&gt;* 頁面。

    ![新增群組成員，與 + 號反白顯示](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. 搜尋您要新增的成員從**新增成員**方塊，然後按**新增**。

    ![新增成員 方塊中，與要加入的新成員](media/my-apps-portal/my-apps-portal-add-member-page.png)

    若要開始存取組織的應用程式發佈至新的成員，傳送邀請。

3. 如果您不小心，新增一個成員，或如果成員已離開組織，您可以選取來移除成員**移除成員**上的成員名稱旁邊*&lt;group_name&gt;* 頁面。

    ![移除成員，以反白顯示的 [移除] 連結](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="delete-a-group"></a>刪除群組
您可以隨時刪除任何您自己的群組。 不過，如果您不小心刪除一組您必須建立它並再次新增成員。

### <a name="to-delete-the-group"></a>若要刪除群組
1. 選取您想要永久刪除，然後選取的群組**刪除群組**上*&lt;group_name&gt;* 頁面。

    ![< 群組名稱 > 頁面，以反白顯示 [刪除群組] 連結](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. 選取 **是**上確認訊息。

    永久刪除此群組。 
    
## <a name="join-an-existing-group"></a>加入現有的群組
您可以加入現有的群組，從**群組**頁面。

### <a name="to-join-or-leave-a-group"></a>若要加入或離開群組

1. 在 **群組**頁面上，選取**加入群組**從**我加入的群組**區域。

    **加入群組**頁面隨即出現。

    ![加入群組 頁面中，加入群組 按鈕反白顯示](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. 在 **加入群組**頁面上，選取您想要加入、 檢視相關聯的群組詳細資料，以及群組是否可用，然後選取群組的名稱**加入群組**。

    如果群組需要核准的成員資格的群組擁有者，您輸入的業務理由為何您要加入群組中，系統會要求，然後選取**要求**。 如果群組不需要核准，您立即新增為成員的群組會出現在您**我加入的群組**清單。

3. 如果您不小心加入群組，或如果您不再需要它的一部分，您可以選取群組名稱，從**加入群組**頁面，然後再選取**離開群組**。

    ![加入群組 頁面中，反白顯示的保留群組按鈕](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>後續步驟

- [存取和使用 My Apps 入口網站上的應用程式](my-apps-portal-end-user-access.md)。

- [變更您的設定檔資訊](my-apps-portal-end-user-update-profile.md)。

- [執行您自己的存取權檢閱](my-apps-portal-end-user-access-reviews.md)。