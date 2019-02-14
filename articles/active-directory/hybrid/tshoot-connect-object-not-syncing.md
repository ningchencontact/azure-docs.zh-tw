---
title: 針對未同步至 Azure AD 的物件進行疑難排解 | Microsoft Docs
description: 針對物件未同步至 Azure AD 的原因進行疑難排解。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55668b8ef8019e1ee808bc0cba9d98c0db53c584
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198735"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>針對未同步至 Azure AD 的物件進行疑難排解

如果物件未如預期般同步至 Azure AD，則可能是數個原因所造成。 如果您從 Azure AD 收到錯誤電子郵件，或是在 Azure AD Connect Health 中看到錯誤，則請改為閱讀[針對匯出錯誤進行疑難排解](tshoot-connect-sync-errors.md)。 但如果您要針對物件不在 Azure AD 中的問題進行疑難排解，則本主題正適合您。 它說明如何尋找內部部署元件 Azure AD Connect 同步中的錯誤。

>[!IMPORTANT]
>對於 1.1.749.0 版或更新版本的 Azure Active Directory (AAD) Connect 部署，請在精靈中使用[疑難排解工作](tshoot-connect-objectsync.md)，針對物件同步處理問題進行疑難排解。 

## <a name="synchronization-process"></a>同步處理程序

在調查同步處理問題之前，先讓我們了解一下 **Azure AD Connect** 的同步處理程序：

  ![Azure AD Connect 同步處理程序](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**術語**

* **CS：** 連接器空間，是資料庫中的資料表。
* **MV：** Metaverse，是資料庫中的資料表。
* **AD：** Active Directory
* **AAD：** Azure Active Directory

### <a name="synchronization-steps"></a>**同步處理步驟**
同步處理程序包含下列步驟：

1. **從 AD 匯入：** 系統會將 **Active Directory** 物件帶入到 **AD CS**。

2. **從 AAD 匯入：** 系統會將 **Azure Active Directory** 物件帶入到 **AAD CS**。

3. **同步處理：****輸入同步處理規則**和**輸出同步處理規則**會依優先順序數字以由低至高的順序執行。 若要檢視同步處理規則，您可以從桌面應用程式移至**同步處理規則編輯器**。 **輸入同步化規則**會從 CS 將資料帶入到 MV。 **輸出同步處理規則**會從 MV 將資料帶入到 CS。

4. **匯出至 AD：** 在執行同步處理之後，物件會從 AD CS 匯出到 **Active Directory**。

5. **匯出至 AAD：** 在執行同步處理之後，物件會從 AAD CS 匯出到 **Azure Active Directory**。

## <a name="troubleshooting"></a>疑難排解

為了尋找錯誤，您將依下列順序查看幾個不同的地方：

1. [作業記錄](#operations) - 可供尋找同步引擎在匯入和同步處理時所識別的錯誤。
2. [連接器空間](#connector-space-object-properties) - 可供尋找遺失的物件和同步處理錯誤。
3. [Metaverse](#metaverse-object-properties) - 可供尋找資料相關問題。

請先啟動 [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md)，再開始進行這些步驟。

## <a name="operations"></a>作業
Synchronization Service Manager 中的 [作業] 索引標籤是您應該開始進行疑難排解的地方。 [作業] 索引標籤顯示最新作業的結果。  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/operations.png)  

上半部會依時間順序顯示所有執行。 根據預設，作業記錄會保留最後 7 天的相關資訊，但是您可以利用 [排程器](how-to-connect-sync-feature-scheduler.md)來變更此設定。 若您想要尋找任何未顯示成功狀態的執行。 您可以按一下標頭來變更排序。

[狀態]  欄位是最重要的資訊，並顯示最嚴重的執行問題。 以下快速摘要依照優先順序來調查的最常見狀態 (其中 * 表示數個可能的錯誤字串)。

| 狀態 | 註解 |
| --- | --- |
| stopped-* |執行無法完成。 例如，如果遠端系統已關閉且無法聯繫。 |
| stopped-error-limit |有 5,000 個以上的錯誤。 執行因錯誤數量過多而自動停止。 |
| completed-\*-errors |執行已完成，但發生應調查的錯誤 (數量少於 5,000 個)。 |
| completed-\*-warnings |執行完成，但某些資料並未處於預期的狀態。 如果您遇到錯誤，則此訊息通常只是一個徵狀。 在您解決錯誤之前，不應該調查警告。 |
| 成功 |沒有問題。 |

當您選取某個資料列時，底部會更新以顯示該執行的詳細資料。 在底部的最左邊，可能會有一份顯示 [步驟 #] 的清單。 如果您的樹系中有多個網域，而每個網域都以一個步驟來代表，則只會顯示此清單。 您可以在 [分割區] 標題下方找到網域名稱。 在 [同步處理統計資料] 下方，您可以找到關於已處理的變更次數詳細資訊。 您可以按一下連結，以取得變更的物件清單。 如果您有物件發生錯誤，這些會顯示於 [同步處理錯誤] 下方。

### <a name="troubleshoot-errors-in-operations-tab"></a>疑難排解 [作業] 索引標籤中的錯誤
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
當您遇到錯誤時，發生錯誤的物件及錯誤本身都會是連結，可提供更多資訊。

首先按一下錯誤字串 (圖片中的**sync-rule-error-function-triggered** )。 您會先看到物件的概觀。 若要查看實際的錯誤，可按一下 [堆疊追蹤] 按鈕。 此追蹤會提供錯誤的偵錯層級資訊。

您可以在 [呼叫堆疊資訊] 方塊上按一下滑鼠右鍵、選擇 [全選]，然後按一下 [複製]。 您接著可以複製堆疊，並在您喜好的編輯器 (例如 [記事本]) 中查看此錯誤。

* 如果錯誤來自 **SyncRulesEngine**，則呼叫堆疊資訊首先具備物件上所有屬性的清單。 向下捲動，直到您看到 **InnerException =>** 標題為止。  
  ![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
   下一行會顯示錯誤。 在上圖中，錯誤是來自所建立的自訂同步處理規則 Fabrikam。

如果錯誤本身未提供足夠的資訊，則請查看資料本身。 您可以按一下帶有物件識別碼的連結，然後繼續針對[連接器空間匯入的物件](#cs-import)進行疑難排解。

## <a name="connector-space-object-properties"></a>連接器空間物件屬性
如果您在 [[作業]](#operations) 索引標籤中沒有任何找到的錯誤，則下一個步驟是依循連接器空間物件從 Active Directory 到 Metaverse，再到 Azure AD。 在此路徑中，您應該會找到問題的所在。

### <a name="search-for-an-object-in-the-cs"></a>在 CS 中搜尋物件

在 [Synchronization Service Manager] 中，按一下 [連接器]、選取 [Active Directory 連接器]，然後選取 [搜尋連接器空間]。

在 [範圍] 中，選取 [RDN] \(當您想要依據 CN 屬性進行搜尋時) 或 [DN 或錨點] \(當您想要依據 distinguishedName 屬性進行搜尋時)。 輸入值，然後按一下 [搜尋]。  
![連接器空間搜尋](./media/tshoot-connect-object-not-syncing/cssearch.png)  

如果您找不到所要尋找的物件，則它可能是已被[網域型篩選](how-to-connect-sync-configure-filtering.md#domain-based-filtering)或 [OU 型篩選](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)所篩選掉。 請閱讀[設定篩選](how-to-connect-sync-configure-filtering.md)主題，以確認篩選是按照預期方式設定。

另一個實用的搜尋是選取 [Azure AD 連接器]，然後在 [範圍] 中選取 [擱置匯入]，接著選取 [新增] 核取方塊。 此搜尋會提供您 Azure AD 中無法與內部部署物件建立關聯的所有已同步處理物件。  
![連接器空間搜尋孤立物件](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
這些物件是由另一個同步引擎所建立，或是由具有不同篩選組態的同步引擎所建立。 此檢視是一個由不再受控「孤立」物件所組成的清單。 您應該檢閱此清單，然後考慮使用 [Azure AD PowerShell](https://aka.ms/aadposh) Cmdlet 來移除這些物件。

### <a name="cs-import"></a>CS 匯入
當您開啟 cs 物件時，頂端會出現數個索引標籤。 [匯入]  索引標籤會顯示匯入後暫存的資料。  
![CS 物件](./media/tshoot-connect-object-not-syncing/csobject.png)    
[舊值] 顯示目前儲存在 Connect 中的值，而 [新值] 則顯示從來源系統收到但尚未套用的值。 如果物件上發生錯誤，系統便不會處理變更。

**錯誤**  
![CS 物件](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
只有當物件發生問題時，才會顯示 [同步處理錯誤] 索引標籤。 如需詳細資訊，請參閱[針對同步處理錯誤進行疑難排解 (英文)](#troubleshoot-errors-in-operations-tab)。

### <a name="cs-lineage"></a>CS 歷程
[歷程] 索引標籤會顯示連接器空間物件與 Metaverse 物件關聯的方式。 您可以看到連接器上次從連接的系統匯入變更的時間，以及套用哪些規則以便在 metaverse 中填入資料。  
![CS 歷程](./media/tshoot-connect-object-not-syncing/cslineage.png)  
在 [動作] 欄中，您可以看到有一個動作為 [佈建] 的 [輸入] 同步處理規則。 這表示，只要此連接器空間物件存在，就會保留 metaverse 物件。 如果同步處理規則清單顯示的同步處理規則方向為 [輸出] 和 [佈建]，這表示刪除 Metaverse 物件時會刪除此物件。  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
在 [PasswordSync] 欄中，您也可以看到輸入連接器空間可對密碼進行變更，因為有一個同步處理規則的值為 **True**。 此密碼接著會透過輸出規則傳送至 Azure AD。

從 [歷程] 索引標籤，您可以按一下 [Metaverse 物件屬性](#mv-attributes)，以移至 Metaverse。

所有索引標籤的底部都有兩個按鈕︰[預覽] 和 [記錄]。

### <a name="preview"></a>預覽
[預覽] 頁面可用來同步處理某一個單一物件。 如果您正在針對某些自訂的同步規則進行疑難排解，而想要查看變更對單一物件的影響，則此頁面會相當有用。 您可以在 [完整同步處理] 和 [差異同步處理] 之間做選擇。您也可以在 [產生預覽] \(這只會在記憶體中保留變更) 和 [認可預覽] \(這會更新 Mataverse 並暫存對目標連接器空間所做的一切變更) 之間做選擇。  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/preview.png)  
您可以檢查物件，以及哪一個規則適用於特定的屬性流程。  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>記錄檔
[記錄] 頁面可用來查看密碼同步狀態和歷程記錄。 如需詳細資訊，請參閱[針對密碼雜湊同步處理進行疑難排解](tshoot-connect-password-hash-synchronization.md)。

## <a name="metaverse-object-properties"></a>Metaverse 物件屬性
通常最好是從來源 Active Directory 連接器空間開始搜尋。 但是您也可以從 Metaverse 開始搜尋。

### <a name="search-for-an-object-in-the-mv"></a>在 MV 中搜尋物件
在 [Synchronization Service Manager] 中，按一下 [Metaverse 搜尋]。 建立一個您知道可以找到使用者的查詢。 您可以搜尋通用的屬性，例如 accountName (sAMAccountName) 和 userPrincipalName。 如需詳細資訊，請參閱 [Metaverse 搜尋](how-to-connect-sync-service-manager-ui-mvsearch.md)。
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

在 [搜尋結果] 視窗中，按一下該物件。

如果您沒有找到該物件，表示它尚未抵達 Metaverse。 請繼續在 **Active Directory** [連接器空間](#connector-space-object-properties)中搜尋該物件。 如果您在 **Active Directory** 連接器空間中發現該物件，則表示可能有來自同步處理的錯誤導致物件無法到達 Metaverse，或是可能套用了某個同步處理規則範圍篩選器。

### <a name="object-not-found-in-the-mv"></a>MV 中找不到物件
如果物件位於 **Active Directory** CS 中，但是不存在於 MV 中，則會套用範圍篩選器。 

* 若要查看範圍篩選器，請移至桌面應用程式的功能表，並按一下 [同步處理規則編輯器]。 藉由調整以下篩選器，來篩選物件適用的規則。

  ![輸入同步處理規則搜尋](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

* 檢視上述清單中的每個規則，並檢查**範圍篩選器**。 在下列範圍篩選器中，如果 **isCriticalSystemObject** 值為 Null、FALSE 或空白，則其在範圍內。

  ![輸入同步處理規則搜尋](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

* 移至 [CS 匯入](#cs-import)屬性清單，並檢查是哪一個篩選器在阻止物件移到 MV。 **連接器空間**屬性清單的該部分只會顯示非 Null 或非空白的屬性。 例如，如果 **isCriticalSystemObject** 未在清單中顯示出來，則表示此屬性的值為 Null 或空白。

### <a name="object-not-found-in-the-aad-cs"></a>AAD CS 中找不到物件
如果物件不存在於 **Azure Active Directory** 的**連接器空間**中。 但卻存在於 MV 中，則請查看對應**連接器空間****輸出**規則的範圍篩選器，並檢查該物件是否因為 [MV屬性](#mv-attributes)不符合準則而篩選掉。

* 若要查看輸出範圍篩選器，請藉由調整以下篩選器來選取該物件適用的規則。 檢視每個規則，並查看對應的 [MV 屬性](#mv-attributes)值。

  ![輸出同步處理規則搜尋](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV 屬性
在 [屬性] 索引標籤上，您可以看到值，以及是由哪一個連接器提供它。  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvobject.png)  

如果物件並未進行同步處理，則請查看 Metaverse 中的下列屬性：
- **cloudFiltered** 屬性是否存在且設定為 **true**？ 如果是，則表示已根據[屬性型篩選](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)中的步驟將它篩除。
- **sourceAnchor** 屬性是否存在？ 如果不存在，您是否擁有帳戶資源樹系拓撲？ 如果物件被識別為已連結的信箱 (**msExchRecipientTypeDetails** 屬性的值為 2)，則會由具有已啟用 Active Directory 帳戶的樹系提供 sourceAnchor。 請確定已正確匯入並同步處理主要帳戶。 主要帳戶必須列在物件的[連接器](#mv-connectors)清單中。

### <a name="mv-connectors"></a>MV 連接器
[連接器] 索引標籤會顯示所有具有物件表示法的連接器空間。  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
您應該要有連至下列各項的連接器：

- 每個呈現出使用者的 Active Directory 樹系。 這項呈現可以包括 foreignSecurityPrincipals 和 Contact 物件。
- Azure AD 中的連接器。

如果您沒有連至 Azure AD 的連接器，則請閱讀 [MV 屬性](#mv-attributes)，以確認佈建到 Azure AD 所需符合的準則。

此索引標籤也可讓您瀏覽至[連接器空間物件](#connector-space-object-properties)。 請選取一個資料列，然後按一下 [屬性]。

## <a name="next-steps"></a>後續步驟
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什麼是混合式身分識別？](whatis-hybrid-identity.md)。
