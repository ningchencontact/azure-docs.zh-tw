---
title: "Azure AD Connect 同步處理：設定 Office 365 中多地理位置功能的慣用資料位置 | Microsoft Docs"
description: "說明如何利用 Azure AD Connect 同步處理，將 Office 365 使用者資源放在使用者附近。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: billmath
ms.openlocfilehash: 8a36fc45334a2f1d12e6eabbfb16731ccc9998bf
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure AD Connect 同步處理：設定 Office 365 資源的慣用資料位置
本主題的目的在於逐步解說如何在 Azure AD Connect 同步處理中設定 PreferredDataLocation。當客戶在 Office 365 中使用多地理位置功能時，可使用這個屬性來指定使用者 Office 365 資料的地理位置。

> [!IMPORTANT]
> 多地理位置功能目前為預覽狀態。 如果您想要加入預覽計畫，請連絡您的 Microsoft 代表。
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>啟用 PreferredDataLocation 的同步處理
根據預設，使用者的 Office 365 資源會與 Azure AD 租用戶位於相同區域。 例如，如果您的租用戶位於北美洲，則使用者的 Exchange 信箱也會位於北美洲。 對於跨國組織，這可能不是最理想的情況。 藉由設定 preferredDataLocation 屬性，即可定義使用者的區域。

藉由設定這個屬性，可以讓使用者的 Office 365 資源 (例如信箱、OneDrive) 位於與使用者相同的區域，且整個組織仍有一個租用戶。

> [!IMPORTANT]
> 為了要使用多地理位置功能，您的 Office 365 訂用帳戶中必須至少有 5000 個基座。
>
>

Office 365 中適用多地理位置功能的區域如下：

| 區域 | 說明 |
| --- | --- |
| NAM | 北美洲 |
| 歐元 | 歐洲 |
| APC | 亞太地區 |
| JPN | 日本 |
| AUS | 澳大利亞 |
| CAN | 加拿大 |
| GBR | 英國 |
| LAM | 拉丁美洲 |

並非所有 Office 365 工作負載都支援設定使用者的區域。

Azure AD Connect 可對 1.1.524.0 版和更新版本之**使用者**物件的 **PreferredDataLocation** 屬性進行同步處理。 更具體地說，我們已導入下列變更︰

* 已擴充 Azure AD 連接器中 [使用者] 物件類型的結構描述，進而納入 PreferredDataLocation 屬性，此屬性的類型是單一值字串。
* Metaverse 中**人員**物件類型的結構描述已擴充而納入了 PreferredDataLocation 屬性，此屬性是字串類型，且具備單一值。

同步處理預設不啟用 PreferredDataLocation 屬性。 這項功能適用於較大型組織，不是每個人都可從中獲益。 您也必須找出一個屬性來保留使用者的 Office 365 地區，因為內部部署 Active Directory 中沒有 PreferredDataLocation 屬性。 這在每個組織中皆不同。

> [!IMPORTANT]
> Azure AD 目前允許使用 Azure AD PowerShell，直接設定「已同步處理的使用者物件」和「雲端使用者物件」上的 PreferredDataLocation 屬性。 在啟用了 PreferredDataLocation 屬性的同步處理之後，您就必須停止使用 Azure AD PowerShell 來設定**已同步處理的使用者物件**上的屬性，因為 Azure AD Connect 會根據內部部署 Active Directory 中的來源屬性值將這些物件加以覆寫。

> [!IMPORTANT]
> 自 2017 年 9 月 1 日起，Azure AD 不再允許使用 Azure AD PowerShell 直接設定**已同步處理的使用者物件**上的 PreferredDataLocation 屬性。 若要設定「已同步處理的使用者物件」上的 PreferredLocation 屬性，您必須使用 Azure AD Connect。

在啟用 PreferredDataLocation 屬性的同步處理之前，您必須︰

* 首先，決定要使用哪個內部部署 Active Directory 屬性來作為來源屬性。 此屬性的類型必須是**單一值字串**。 以下步驟會使用其中一個 extensionAttribute。
* 如果您先前已使用 Azure AD PowerShell，來設定 Azure AD 中現有「已同步處理的使用者物件」上的 PreferredDataLocation 屬性，則必須將這些屬性值**向下移植**到內部部署 Active Directory 中對應的使用者物件內。

    > [!IMPORTANT]
    > 如果您未將這些屬性值向下移植到內部部署 Active Directory 中對應的使用者物件，Azure AD Connect 將會在 PreferredDataLocation 屬性的同步處理啟用時，移除 Azure AD 中現有的屬性值。

* 目前我們會建議您在至少幾個內部部署 AD 使用者物件上設定來源屬性，以供稍後用來進行驗證。

為 PreferredDataLocation 屬性啟用同步處理的步驟可總結為以下幾項︰

1. 停用同步排程器，並確認沒有任何同步處理在進行中
2. 在內部部署 ADDS 連接器結構描述中新增來源屬性
3. 在 Azure AD 連接器結構描述中新增 PreferredDataLocation
4. 建立輸入同步處理規則，以從內部部署 Active Directory 傳輸屬性值
5. 建立輸出同步處理規則，以將屬性值傳輸到 Azure AD
6. 執行完整的同步處理週期
7. 啟用同步排程器
8. 確認結果

> [!NOTE]
> 本節的剩餘內容會詳述這些步驟。 用來說明這些步驟的環境是某個 Azure AD 部署，其具有單一樹系拓撲，但沒有自訂的同步處理規則。 如果您設定了多樹系拓撲和自訂同步處理規則，或具有預備伺服器，則需要據此現況調整這些步驟。

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>步驟 1：停用同步排程器，並確認沒有任何同步處理在進行中
請確定在您更新同步處理規則時系統不會進行同步處理，以免將不想要的變更匯出到 Azure AD。 若要停用內建的同步排程器︰

1. 在 Azure AD Connect 伺服器上啟動 PowerShell 工作階段。
2. 執行下列 Cmdlet 停用排定的同步處理︰`Set-ADSyncScheduler -SyncCycleEnabled $false`。
3. 移至 [開始]  >  [同步處理服務] 來啟動 **Synchronization Service Manager**。
4. 移至 [作業] 索引標籤，確認沒有任何作業是「進行中」狀態。

![同步處理服務管理員 - 確認沒有進行中的作業](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>步驟 2：在內部部署 ADDS 連接器結構描述中新增來源屬性
並非所有 AD 屬性都會匯入至內部部署 AD 連接器空間。 如果您選擇使用的屬性預設不要同步處理，則需自行將它匯入。 若要在所匯入屬性的清單中新增來源屬性︰

1. 移至 Synchronization Service Manager 中的 [連接器] 索引標籤。
2. 以滑鼠右鍵按一下 [內部部署 AD 連接器]，然後選取 [屬性]。
3. 在彈出的對話方塊中，移至 [選取屬性] 索引標籤。
4. 確定您已在屬性清單中，勾選您選擇使用的來源屬性。 如果看不到您的屬性，則按一下 [全部顯示] 核取方塊。
5. 按一下 [確定] 來進行儲存。

![在內部部署 AD 連接器結構描述中新增來源屬性](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>步驟 3：在 Azure AD 連接器結構描述中新增 PreferredDataLocation
根據預設，系統不會將 PreferredDataLocation 屬性匯入 Azure AD 連接器空間中。 若要在所匯入屬性的清單中新增 PreferredDataLocation 屬性︰

1. 移至 Synchronization Service Manager 中的 [連接器] 索引標籤。
2. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [屬性]。
3. 在彈出的對話方塊中，移至 [選取屬性] 索引標籤。
4. 選取屬性清單中的 preferredDataLocation 屬性。
5. 按一下 [確定] 來進行儲存。

![在 Azure AD 連接器結構描述中新增來源屬性](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>步驟 4：建立輸入同步處理規則，以從內部部署 Active Directory 傳輸屬性值
輸入同步處理規則允許屬性值從內部部署 Active Directory 的來源屬性傳輸到 Metaverse︰

1. 移至 [開始]  >  [同步處理規則編輯器] 來啟動**同步處理規則編輯器**。
2. 將搜尋篩選條件的 [方向] 設定為 [輸入]。
3. 按一下 [新增規則] 按鈕以建立新的輸入規則。
4. 在 [描述] 索引標籤下，提供下列設定︰

    | 屬性 | 值 | 詳細資料 |
    | --- | --- | --- |
    | Name | 提供名稱 | 例如，「In from AD – User PreferredDataLocation」 |
    | 說明 | *提供自訂描述* |  |
    | 連線系統 | 選取內部部署 AD 連接器 |  |
    | 連線系統物件類型 | **使用者** |  |
    | Metaverse 物件類型 | **人員** |  |
    | 連結類型 | **Join** |  |
    | 優先順序 | 選擇 1 - 99 之間的數字 | 1 - 99 已保留供自訂同步處理規則使用。 請勿挑選已由其他同步處理規則使用的值。 |

5. [範圍篩選器] 保持空白，以便包含所有物件。 您可能需要根據 Azure AD Connect 部署來調整範圍篩選器。
6. 移至 [轉換] 索引標籤，並實作下列轉換規則︰

    | 流程類型 | 目標屬性 | 來源 | 套用一次 | 合併類型 |
    | --- | --- | --- | --- | --- |
    |直接 | PreferredDataLocation | 挑選來源屬性 | 未核取 | 更新 |

7. 按一下 [新增] 來建立輸入規則。

![建立輸入同步處理規則](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>步驟 5：建立輸出同步處理規則，以將屬性值傳輸到 Azure AD
輸出同步處理規則允許屬性值從 Metaverse 傳輸到 Azure AD 中的 PreferredDataLocation 屬性︰

1. 移至**同步處理規則**編輯器。
2. 將搜尋篩選條件的 [方向] 設定為 [輸出]。
3. 按一下 [新增規則] 按鈕。
4. 在 [描述] 索引標籤下，提供下列設定︰

    | 屬性 | 值 | 詳細資料 |
    | ----- | ------ | --- |
    | Name | 提供名稱 | 例如，「Out to AAD – User PreferredDataLocation」 |
    | 說明 | 提供描述 ||
    | 連線系統 | 選取 AAD 連接器 ||
    | 連線系統物件類型 | User ||
    | Metaverse 物件類型 | **人員** ||
    | 連結類型 | **Join** ||
    | 優先順序 | 選擇 1 - 99 之間的數字 | 1 - 99 已保留供自訂同步處理規則使用。 請勿挑選已由其他同步處理規則使用的值。 |

5. 移至 [範圍篩選器] 索引標籤，並**使用兩個子句來新增單一範圍篩選器群組**：

    | 屬性 | 運算子 | 值 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | User |
    | cloudMastered | NOTEQUAL | True |

    範圍篩選器會決定此輸出同步處理規則要套用至哪個 Azure AD 物件。 在此範例中，我們會使用來自「Out to AD – User Identity」OOB 同步處理規則的同一個範圍篩選器。 它可避免同步處理規則套用到並非從內部部署 Active Directory 同步過來的使用者物件。 您可能需要根據 Azure AD Connect 部署來調整範圍篩選器。

6. 移至 [轉換] 索引標籤，並實作下列轉換規則︰

    | 流程類型 | 目標屬性 | 來源 | 套用一次 | 合併類型 |
    | --- | --- | --- | --- | --- |
    | 直接 | PreferredDataLocation | PreferredDataLocation | 未核取 | 更新 |

7. 關閉 [新增] 來建立輸出規則。

![建立輸出同步處理規則](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>步驟 6：執行完整的同步處理週期
由於我們已在 AD 和 Azure AD 連接器結構描述中同時新增屬性，並導入了自訂同步處理規則，因此一般來說，我們必須經歷完整的同步處理週期。 建議您先驗證變更，再將它們匯出至 Azure AD。 在手動執行構成完整同步處理週期的步驟時，您可以使用下列步驟來驗證變更。

1. 在**內部部署 AD 連接器**上執行**完整匯入**步驟：

   1. 移至 Synchronization Service Manager 中的 [作業] 索引標籤。
   2. 以滑鼠右鍵按一下 [內部部署 AD 連接器]，然後選取 [執行...]。
   3. 在彈出的對話方塊中選取 [完整匯入]，然後按一下 [確定]。
   4. 等候作業完成。

    > [!NOTE]
    > 如果所匯入屬性的清單中已包含來源屬性，您可以略過內部部署 AD 連接器上的完整匯入。 換句話說，在[步驟 2：在內部部署 AD 連接器結構描述中新增來源屬性](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema)期間，您不必進行任何變更。

2. 在 **Azure AD 連接器**上執行**完整匯入**步驟：

   1. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [執行...]
   2. 在彈出的對話方塊中選取 [完整匯入]，然後按一下 [確定]。
   3. 等候作業完成。

3. 驗證現有使用者物件上的同步處理規則變更︰

來自內部部署 Active Directory 的來源屬性和來自 Azure AD 的 PreferredDataLocation 都已匯入個別的連接器空間中。 在繼續進行「完整的同步處理」步驟之前，建議您先**預覽**內部部署 AD 連接器空間中的現有使用者物件。 您所挑選的物件應該要填入來源屬性。 若能成功**預覽**到 Metaverse 中已填入 PreferredDataLocation，則表示您已正確設定同步處理規則。 如需如何進行**預覽**的相關資訊，請參閱[驗證變更](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change)一節。

4. 在**內部部署 AD 連接器**上執行**完整同步處理**步驟：

   1. 以滑鼠右鍵按一下 [內部部署 AD 連接器]，然後選取 [執行...]。
   2. 在彈出的對話方塊中選取 [完整同步處理]，然後按一下 [確定]。
   3. 等候作業完成。

5. 確認要對 Azure AD 執行的**擱置匯出**：

   1. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [搜尋連接器空間]。
   2. 在彈出的 [搜尋連接器空間] 對話方塊中：

      1. 將 [範圍] 設定為 [擱置匯出]。
      2. 將 3 個核取方塊全部勾選，包括 [新增]、[修改] 和 [刪除]。
      3. 按一下 [搜尋] 按鈕以取得有變更要匯出的物件清單。 若要檢查給定物件的變更，請對物件按兩下。
      4. 確認變更符合預期。

6. 在 **Azure AD 連接器**上執行**完整匯出**步驟

   1. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [執行...]。
   2. 在彈出的 [執行連接器] 對話方塊中選取 [匯出]，然後按一下 [確定]。
   3. 等待「匯出至 Azure AD」完成。

> [!NOTE]
> 您可能會發現，Azure AD 連接器的步驟並未包含「完整同步處理」步驟和「匯出」步驟。 屬性值只會從內部部署 Active Directory 傳輸到 Azure AD，因此 Azure AD 不需要這些步驟。

## <a name="step-7-re-enable-sync-scheduler"></a>步驟 7：重新啟用同步排程器
重新啟用內建的同步排程器︰

1. 啟動 PowerShell 工作階段。
2. 執行 Cmdlet 以重新啟用排定的同步處理︰`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>步驟 8：確認結果
現在該來確認組態，並且為您的使用者啟用它。

1. 將區域新增至使用者的所選屬性。 [本表格](#enable-synchronization-of-preferreddatalocation)提供可用的區域清單。  
![已新增至使用者的 AD 屬性](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. 等待屬性與 Azure AD 同步。
3. 使用 Exchange Online PowerShell，確認已正確設定信箱區域。  
![Exchange Online 使用者的信箱區域設定](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
假設租用戶已標示為能夠使用這項功能，則信箱會移至正確的區域。 這可藉由查看信箱所在的伺服器名稱來確認。
4. 若要確認這項設定已在許多信箱上生效，請使用 [Technet 資源庫](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e)中的指令碼。 此指令碼也有一份所有 Office 365 資料中心伺服器前置詞以及這些資料中心所在區域的清單。 這份清單可以作為前一個步驟的參考，用來確認信箱的位置。

## <a name="next-steps"></a>後續步驟

**深入了解 Office 365 的多地理位置功能：**

* Ignite 上的多地理位置工作階段：https://aka.ms/MultiGeoIgnite
* OneDrive 的多地理位置功能：https://aka.ms/OneDriveMultiGeo
* SharePoint Online 的多地理位置功能：https://aka.ms/SharePointMultiGeo

**深入了解同步處理引擎的組態模型：**

* 如需組態模型的詳細資訊，請參閱 [了解宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)。
* 如需運算式語言的詳細資訊，請參閱 [了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)。

**概觀主題**

* [Azure AD Connect 同步處理：了解及自訂同步處理](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)
