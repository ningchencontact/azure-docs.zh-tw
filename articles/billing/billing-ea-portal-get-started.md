---
title: 開始使用 Azure EA 入口網站
description: 本文將說明 Azure EA 客戶可以如何使用 Azure EA 入口網站。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900925"
---
# <a name="get-started-with-the-azure-ea-portal"></a>開始使用 Azure EA 入口網站

本文可協助直接與間接的 Azure EA 客戶開始使用 [Azure EA 入口網站](https://ea.azure.com)，其中包含以下基本資訊：

- Azure EA 入口網站的結構為何。
- Azure EA 入口網站中使用的角色。
- 如何開始建立訂用帳戶。
- 分析 Azure EA 入口網站和 Azure 入口網站中的成本。

以下影片將示範完整的 Azure EA 入口網站上線工作階段：

[Azure EA 入口網站上線影片](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Azure EA 入口網站階層

Azure EA 入口網站階層包含：

**Microsoft Azure EA 入口網站** - Azure EA 入口網站是一個線上管理入口網站，可協助您管理 Azure EA 服務的成本。 您可以使用此入口網站來建立 Azure EA 階層，其中包括部門、帳戶和訂用帳戶。 您也可以將其用於協調已取用服務的成本、下載使用量報告，以及查看價格清單。 此外，您將建立用於註冊的 API 金鑰。

**部門** - 建立部門可協助將成本分割成邏輯群組，然後在部門層級上設定預算或配額。

**帳戶** – 帳戶是 Azure EA 入口網站中的組織單位，用來管理訂用帳戶。 帳戶也會用於報告。

**訂用帳戶** – 訂用帳戶是 Azure EA 入口網站中的最小單位。 這些訂用帳戶是由服務管理員所管理的 Azure 服務容器。

下圖說明簡單的 Azure EA 階層。

![簡單 Azure EA 階層的圖表](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>企業使用者角色

若要管理您註冊中的 Azure 服務，有 4 個不同的企業系統管理使用者角色可使用：

- 企業系統管理員
- 部門系統管理員
- 帳戶擁有者
- 服務管理員

角色會用來在兩個不同的 Microsoft Azure 入口網站中完成工作。 Azure EA 入口網站 (https://ea.azure.com) 用來協助您管理帳單和成本。 Azure 入口網站 (https://portal.azure.com) 用來管理 Azure 服務。

使用者角色會與使用者帳戶相關聯。 若要驗證使用者的真確性，每位使用者都必須擁有有效的公司、學校或 Microsoft 帳戶。 請確定每個帳戶都與主動監視的電子郵件地址相關聯。 帳戶通知會傳送至該電子郵件地址。

設定使用者時，您可以將多個公司、學校或 Microsoft 帳戶指派給企業系統管理員角色。 不過，您只能將一個公司、學校或 Microsoft 帳戶指派給帳戶擁有者角色。 此外，單一公司、學校或 Microsoft 帳戶可以同時套用「企業系統管理員」和「帳戶擁有者」角色。

### <a name="enterprise-administrator"></a>企業系統管理員

企業系統管理員角色具有最高層級的存取權。 具有此角色的使用者可以：

- 管理帳戶和帳戶擁有者
- 管理其他企業系統管理員
- 管理部門系統管理員
- 管理通知連絡人
- 檢視所有帳戶上的使用量
- 檢視所有帳戶上未開立帳單的費用

一個企業註冊中可以有多個企業系統管理員。 您可以將唯讀存取權授與企業系統管理員。 他們都會繼承部門系統管理員角色。

### <a name="department-administrator"></a>部門系統管理員

具有此角色的使用者可以：

- 建立和管理部門
- 建立新的帳戶擁有者
- 針對他們所管理的部門檢視使用量詳細資料
- 檢視成本 (若已獲得必要權限)

每個企業註冊可以有多個部門系統管理員。

您可以授與部門系統管理員唯讀存取權。 若要授與唯讀存取權，請編輯或建立新的部門系統管理員，並將唯讀選項設為 [是]  。

### <a name="account-owner"></a>帳戶擁有者

具有此角色的使用者可以：

- 建立及管理訂用帳戶
- 管理服務管理員
- 檢視訂用帳戶的使用量

每個帳戶都需要唯一的公司、學校或 Microsoft 帳戶。 如需有關 Azure EA 入口網站系統管理角色的詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。

### <a name="service-administrator"></a>服務管理員

服務管理員有權管理 Azure 入口網站中的服務，並可將使用者指派給共同管理員角色。

## <a name="activate-your-enrollment"></a>啟動您的註冊

若要啟動您的服務，最初的企業系統管理員需從邀請電子郵件中開啟 [https://ea.azure.com](https://ea.azure.com) 上的 Azure EA 入口網站，並使用電子郵件地址登入。

如果您有多個註冊，請選擇一個進行啟用。 預設只會顯示作用中的註冊。 若要檢視註冊記錄，請清除 Azure EA 入口網站右上方的 [作用中]  選項。

在 [註冊] 底下，狀態會顯示 [作用中]  。

![顯示作用中註冊的範例](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

只有現有的 Azure 企業系統管理員可以建立其他企業系統管理員。

### <a name="create-another-enterprise-admin"></a>建立另一個企業系統管理員

- 登入 [Azure EA 入口網站](https://ea.azure.com)並流覽至 [管理]   > [註冊詳細資料]  ，然後按一下頁面右上角的 [+ 新增系統管理員]  。

請確定您有使用者的電子郵件地址和慣用的驗證方法，例如公司或學校驗證，或是 Microsoft 帳戶。 您需要資訊才能新增使用者。

如果您不是 EA 系統管理員，請洽詢 EA 系統管理員，讓他們將您新增至註冊。 新增至註冊之後，您會收到啟用電子郵件。

如果您的 EA 系統管理員無法協助您，請建立 [Azure EA 入口網站支援要求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 請提供下列資訊：

- 註冊號碼
- 要新增的電子郵件地址及驗證類型 (公司、學校或 Microsoft 帳戶)
- 來自現有 EA 系統管理員的電子郵件核准
  - 如果沒有現有的 EA 系統管理員可用，請洽詢您的合作夥伴或軟體顧問，要求他們透過 VLSC 工具變更連絡人詳細資料。

如需有關企業系統管理角色的詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。

## <a name="create-an-azure-ea-department"></a>建立 Azure EA 部門

企業系統管理員和部門系統管理員會使用部門來進行組織，並且依據部門及成本中心報告企業 Azure 服務及使用量。 企業系統管理員可以：

- 新增或移除部門
- 將帳戶與部門建立關聯
- 建立部門系統管理員
- 允許部門系統管理員檢視價格和成本

部門系統管理員可以在其部門中新增帳戶。 他們可以從其部門移除帳戶，但不會從註冊中移除。

若要新增部門：

1. 在左側導覽區域中，按一下 [管理]  。
2. 按一下 [部門]  索引標籤，然後按一下 [+ 新增部門]  ，接著輸入必要的資訊。
3. 部門名稱是唯一的必要欄位。 至少必須有三個字元。
4. 完成時，按一下 [新增]  。

## <a name="add-a-department-admin"></a>新增部門系統管理員

建立部門之後，Azure 企業系統管理員可以新增部門系統管理員，並將其與部門產生關聯。 部門系統管理員可以：

- 建立其他部門系統管理員
- 檢視和編輯部門屬性，例如名稱或成本中心
- 為其部門新增帳戶
- 從其部門移除帳戶
- 下載其部門的使用量詳細資料
- 如果企業系統管理員已授與他們權限，則他們可以檢視其部門的每月使用量和費用 <sup>1</sup>

### <a name="to-add-a-department-admin"></a>若要新增部門系統管理員

企業系統管理員：

1. 在左側導覽區域中，按一下 [管理]  。
2. 按一下 [部門]  索引標籤，然後按一下部門。
3. 按一下 [+ 新增系統管理員]  並新增必要資訊。
4. 如需唯讀存取權，請將 [唯讀]  選項設為 [是]  ，然後按一下 [新增]  。

![顯示 [新增部門系統管理員] 對話方塊的範例](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>若要設定唯讀存取權

您可以將唯讀存取權授與部門系統管理員。 建立新的部門系統管理員時：

- 將 [唯讀] 選項設為 [是]  。

若要編輯現有的部門系統管理員：

1. 選取部門，然後在您想要編輯的 [部門系統管理員]  旁邊，按一下鉛筆符號。
2. 將 [唯讀] 選項設為 [是]  。 然後按一下 [儲存]  。

具有「企業系統管理員」角色的使用者會自動取得部門系統管理員權限。

<sup>1</sup> 如果您已獲得檢視部門每月使用量和費用的權限，卻看不到這些項目，請與您的合作夥伴連絡。

## <a name="add-an-account"></a>新增帳戶

帳戶和訂用帳戶結構會影響其管理方式，以及在您發票和報表上顯示的方式。 一般組織的範例包括依營業單位、功能小組和地理位置進行結構化。

若要新增帳戶：

1. 在 Azure EA 入口網站中，按一下左側導覽區域中的 [管理]  。
2. 按一下 [帳戶]  索引標籤，然後在 [帳戶]  頁面上按一下 [+新增帳戶]  。
3. 選取部門，或將其保留為 [未指派]，然後選取所需的驗證類型。
4. 輸入自訂名稱，以用來識別報表中的帳戶。
5. 輸入要與新帳戶產生關聯的**帳戶擁有者電子郵件**地址。
6. 確認電子郵件地址，然後按一下 [新增]  。

![顯示帳戶清單和 [+ 新增帳戶] 選項的範例](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

您可以按一下 [新增其他帳戶]  ，或按一下左側工具列右下角的 [新增]  來新增另一個帳戶。

若要確認帳戶擁有權：

1. 登入 Azure EA 入口網站。
2. 藉由檢視狀態來確認帳戶擁有權。 狀態應該會從 [擱置]  變更為 [開始/結束日期]  。 開始/結束日期是使用者第一次登入的日期和合約結束日期。


## <a name="change-account-owner"></a>變更帳戶擁有者

企業系統管理員可以使用 Azure EA 入口網站來轉移註冊中的訂用帳戶擁有權。 此動作會將來源使用者帳戶中的所有訂用帳戶移至目的地使用者帳戶。

轉移使用者帳戶資訊的相關要點：

- 支援從公司或學校帳戶轉移至另一個公司或學校帳戶。
- 支援從 Microsoft 帳戶轉移至公司或學校帳戶。
- 支援從公司或學校帳戶轉移至 Microsoft 帳戶。
- 支援從 Microsoft 帳戶轉移至另一個 Microsoft 帳戶。 目標帳戶必須是有效的 Azure Commerce 帳戶，才能作為轉移的有效目標。 若使用新帳戶，當您登入 Azure EA 入口網站時，系統會要求您建立 Azure Commerce 帳戶。 若使用現有帳戶，您必須先建立新的 Azure 訂用帳戶，才能讓該帳戶符合資格。
- 當您完成訂用帳戶轉移時，Microsoft 會更新帳戶擁有者。

RBAC 原則：

- 只有在相同租用戶中的兩個組織識別碼之間轉移 Azure 訂用帳戶，才能保留現有的 Azure 角色型存取控制 (RBAC) 原則、服務管理員角色指派及共同管理員角色指派。 其他訂用帳戶轉移會導致您的 RBAC 原則和服務管理員的共同管理員角色指派遺失。 原則和管理員角色不會在不同的目錄之間轉移。 服務管理員會更新為目的地帳戶的擁有者。
- 當您在相同租用戶中的兩個組織識別碼之間執行訂用帳戶轉移時，RBAC 原則和現有的服務管理員及共同管理員角色都會保留。

變更帳戶擁有者之前：

1. 檢視 [帳戶]  索引標籤並識別來源帳戶。 來源帳戶的狀態必須為作用中。
2. 識別目的地帳戶。 必須是作用中的狀態。

若要轉移所有訂用帳戶的帳戶擁有權：

1. 在左側導覽區域中，按一下 [管理]  。
2. 按一下 [帳戶]  索引標籤，並將滑鼠停留在帳戶上。
3. 按一下右側的變更帳戶擁有者符號。 該符號類似一個人型。
4. 選取合格的帳戶，然後按 [下一步]  。
5. 確認轉移，然後按一下 [提交]  。

![顯示變更帳戶擁有者符號的影像](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

若要轉移單一訂用帳戶的帳戶擁有權：

1. 在左側導覽區域中，按一下 [管理]  。
2. 按一下 [帳戶]  索引標籤，並將滑鼠停留在帳戶上。
3. 按一下右側的轉移訂用帳戶符號。 該符號類似一個頁面。
4. 選取合格的訂用帳戶，然後按 [下一步]  。
5. 確認轉移，然後按一下 [提交]  。

![顯示轉移訂用帳戶符號的影像](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

以下影片會示範 Azure EA 入口網站的使用者管理：

[Azure EA 入口網站的使用者管理影片](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>建立訂用帳戶

帳戶擁有者可以檢視和管理訂用帳戶。 您可以使用訂用帳戶將開發環境和專案的存取權提供給組織中的小組。 例如：測試、生產、開發和預備。 當您為每個應用程式環境建立不同的訂用帳戶後，您可以協助保護每個環境的安全。 您也可以為每個訂用帳戶指派不同的服務管理員帳戶。 您可以將訂用帳戶與任意數目的服務建立關聯。 帳戶擁有者會建立訂用帳戶，並將服務管理員帳戶指派給其帳戶中的每個訂用帳戶。

### <a name="add-a-subscription"></a>新增訂用帳戶

使用下列資訊來新增訂用帳戶。

第一次將訂用帳戶新增至您的帳戶時，系統會要求您接受 MOSA 合約和費率方案。 雖然這些不適用於 Enterprise 合約客戶，但這是建立您訂用帳戶的必要項目。 您的 Microsoft Azure Enterprise 合約註冊修訂會取代上述項目，而您的合約關係並不會變更。 出現提示時，請選取表示您接受條款的方塊。

系統會使用預設的訂用帳戶名稱「Microsoft Azure 企業版」  來建立所有新的訂用帳戶名稱。 您可以更新訂用帳戶名稱，使其與您註冊中的其他訂用帳戶有所區別。 此外，也可確保您能在企業層級上的報表中辨識該訂用帳戶。

若要新增訂用帳戶：

1. 在 Azure EA 入口網站中，登入帳戶。
2. 按一下 [系統管理員]  索引標籤，然後按一下頁面頂端的 [訂用帳戶]  。
2. 確認您已以帳戶的帳戶擁有者身分登入。
3. 按一下 [+ 新增訂用帳戶]  ，然後按一下 [購買]  。
  第一次將訂用帳戶新增至帳戶時，您必須提供連絡人資訊。 新增其他訂用帳戶時，系統會為您加入連絡人資訊。
4. 按一下 [訂用帳戶]  ，並選取您建立的訂用帳戶，然後按一下 [編輯訂用帳戶詳細資料]  。
5. 更新**訂用帳戶名稱**和**服務管理員**，然後選取核取記號。
  訂用帳戶名稱會出現在報表上，而且是與開發入口網站中訂用帳戶相關聯的專案名稱。

新的訂用帳戶可能需要最多 24 小時的時間，才會出現在訂用帳戶清單中。 建立訂用帳戶之後，您可以：

- [編輯訂用帳戶詳細資料](https://account.azure.com/Subscriptions)
- [管理訂用帳戶服務](https://portal.azure.com/#home)

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>將隨用隨付訂用帳戶轉移至 EA 訂用帳戶

若要將採隨用隨付費率的個別訂用帳戶轉移到 EA 訂用帳戶，您必須在 Azure 入口網站中建立新的支援要求。 若要建立支援要求，請按一下 [說明及支援] 區域中的 [+ 新增支援要求]  。


## <a name="view-usage-summary-and-download-reports"></a>檢視使用量摘要及下載報表

企業系統管理員可以在 Azure EA 入口網站中檢視其使用量資料的摘要、已使用的預付金，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

若要檢視特定帳戶的詳細使用量

下載使用量詳細資料報表。 按一下 [報表]  ，然後按一下 [下載使用量]  索引標籤。在報表清單中，針對想要取得的每月報表按一下 [下載]  。

報表不包含任何適用的稅金。 從使用量發生到使用量在報表上反映時，可能會有最多 8 小時的延遲時間。

若要檢視使用量摘要報表和圖表：

1. 在 Azure EA 入口網站的左側導覽區域中按一下 [報表]  ，然後檢視 [使用量摘要]  索引標籤。  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. 選取承諾期間。
3. 在頁面右上角的 **M** (每月) 和 **C** (自訂) 之間做切換，以使用自訂的開始和結束日期來查看**使用量摘要**。  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. 選取圖表上的期間或月份來檢視其他詳細資料。
5. 此圖表會顯示每月使用量明細，其中包含已使用的用量、超額服務、個別計費的費用和市集費用。
6. 針對選取的月份，依圖表下方的部門、帳戶和訂用帳戶進行篩選。
7. 在**依服務收費**和**依階層收費**之間做切換。
8. 展開及折疊 **Azure 服務**、**個別計費的費用**及 **Azure Marketplace** 來查看詳細資料。

以下影片會示範如何檢視使用量：

[Azure EA 入口網站的使用量影片](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>下載 CSV 報表

企業系統管理員可以使用每月報表下載頁面，將數個報表下載為 CSV 檔案。 這些包括：

- 餘額和費用
- 使用量詳細資料
- Marketplace 費用
- 價位表

若要下載報表：


1. 在 Azure EA 入口網站中，按一下 [報表]  。
2. 按一下頁面頂端的 [下載使用量]  。
3. 選取月份報表旁的 [下載]  。

產生使用量的時間與使用量顯示在報表中的時間可能會有最多五天的延遲。

使用 Safari 將 CSV 檔案下載至 Excel 的使用者可能會遇到格式錯誤。 若要避免錯誤，請使用文字編輯器來開啟檔案。

![顯示 [下載使用量] 頁面的範例](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

以下影片會示範如何下載使用量資訊：

[Azure EA 入口網站的使用量影片](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>排定上線通話

如果您想要排定親自參與的客戶上線工作階段，請在 [Azure EA 入口網站支援](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133)上建立支援要求。 請選取 [上線]  作為 [問題類別]  。

## <a name="next-steps"></a>後續步驟
- Azure EA 入口網站系統管理員應閱讀 [ Azure EA 入口網站的系統管理](billing-ea-portal-administration.md)，以了解常見的管理工作。
- 如果您需要 Azure EA 入口網站問題的疑難排解協助，請參閱[針對 Azure EA 入口網站的存取進行疑難排解](billing-ea-portal-troubleshoot.md)。
