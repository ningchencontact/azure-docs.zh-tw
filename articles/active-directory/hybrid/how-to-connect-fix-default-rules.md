---
title: 如何修正已修改的預設規則-Azure AD Connect |Microsoft Docs
description: 了解如何修正 Azure AD Connect 隨附的已修改的預設規則。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698153"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>修正 Azure AD Connect 中的已修改的預設規則

Azure AD Connect 隨附預設的同步處理規則。  不幸的是這些規則不會套用通用所有的組織，並可能有時間，當您需要加以修改時，根據您的需求。

 如果您已修改預設規則，或打算以修改它們，請花一點時間閱讀這份文件。

這份文件呈現最常見的自訂項目由使用者 2 的範例，並說明完成這些自訂的正確方式。

>[!NOTE] 
> 修改現有的預設規則，以達到所需的自訂不支援-如此將會導致在未來版本中的最新版本更新這些規則。 這將導致您收到所需的 bug 修正和新功能。  本文件將說明如何達成相同的結果，而不需修改現有的預設規則。 

## <a name="how-to-identify-modified-default-rules"></a>如何找出修改過的預設規則？
開始版本 1.3.7.0 **Azure AD Connect**，現在很容易識別修改過的預設規則。 您可以前往 在桌面上的 應用程式，並按一下**同步處理規則編輯器**。

![編輯器](media/how-to-connect-fix-default-rules/default1.png)

在編輯器中，任何修改過的預設規則將顯示且名稱前面會有圖示，如下所示：

![icon](media/how-to-connect-fix-default-rules/default2.png)

 也會看到已停用的規則具有相同名稱旁邊也就是標準的預設規則：

![預設規則](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常見的自訂項目
以下是常見的自訂的預設規則：

- [變更屬性流程](#changing-attribute-flow)
- [變更範圍篩選器](#changing-scoping-filter)
- [變更聯結條件](#changing-join-condition)

## <a name="before-changing-any-rules"></a>然後再變更任何規則
- 停用同步排程器。  排程器預設會執行每隔 30 分鐘。 確保在進行變更時排程器未啟動，並疑難排解您的新規則。 若要暫時停用排程器，請啟動 PowerShell，然後執行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。
 ![預設規則](media/how-to-connect-fix-default-rules/default3.png)

- 範圍篩選器中的變更可能會導致刪除目標目錄中的物件。 請小心在領域的物件進行任何變更之前。 建議您先到預備伺服器的變更，然後再使用中的伺服器上進行變更。
- 請於中所述，單一物件上執行預覽[驗證同步處理規則](#validate-sync-rule)區段中，新增任何新的規則之後。
- 請新增新的規則，或修改任何自訂同步處理規則之後，執行完整同步處理。 此同步處理會套用至所有物件的新規則。

## <a name="changing-attribute-flow"></a>變更屬性流程
有 3 個不同的案例的屬性流程，讓我們看看如何達成此目的而不需要變更標準的預設規則。
- 加入新的屬性
- 覆寫現有屬性的值
- 不會同步處理現有的屬性

### <a name="adding-new-attribute"></a>加入新的屬性：
如果您發現屬性不會從來源目錄傳輸到目標目錄，則您可以使用[Azure AD Connect 同步：目錄擴充](how-to-connect-sync-feature-directory-extensions.md)流向新的屬性。

請注意您的第一個選擇應為使用[Azure AD Connect 同步：目錄擴充](how-to-connect-sync-feature-directory-extensions.md)，則不使用 Azure AD Connect 所提供的方塊功能。 不過，如果它不適合您，請瀏覽下列步驟來傳送而不需修改現有的標準預設同步處理規則的屬性，您可以藉由新增兩個新的同步處理規則。


#### <a name="add-an-inbound-sync-rule"></a>新增輸入同步處理規則：
輸入同步處理規則表示屬性的來源是連接器空間，而且目標會在 metaverse。 比方說，流程從內部部署 Active Directory 到 Azure Active Directory 的新屬性，建立新的輸入同步處理規則啟動**同步處理規則編輯器**，然後選取 方向**Inbound** ，按一下 **新增規則**。 

 ![預設規則](media/how-to-connect-fix-default-rules/default3a.png)

遵循您自己的命名慣例來命名規則，我們使用了以下**自訂 In from AD-使用者**，就表示規則是自訂的規則，而且會從 AD 連接器空間到 Metaverse 的輸入的規則。   

 ![預設規則](media/how-to-connect-fix-default-rules/default3b.png)

提供規則的描述，讓未來的維護的規則很容易的此規則的目標為何，和需要原因等。
選取 已連線系統 （樹系） 的屬性來源。 然後，選取的連線系統物件類型及 Metaverse 物件類型。

指定優先順序值介於 0 – 99 （較低的數字，較高優先順序）。 保留其他欄位，例如 'Tag'，' 啟用密碼同步處理 ' 和 'Disabled' 為預設值。

保持 「 範圍設定篩選 」 空的這表示規則會套用到已加入 AD 已連線的系統和 Metaverse 之間的所有物件。

保持 「 加入規則 」 的空白表示此規則會利用標準的預設規則中所定義的聯結條件。 這是另一個不停用/刪除標準的預設規則因為如果沒有任何聯結條件，借助然後屬性不會流動的原因。 

新增適當的轉換屬性，您可以指派流向您的目標屬性的常數值的常數，或直接來源或目標屬性或屬性的運算式之間的對應。 以下是各種[運算式函式](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)您可以使用。

#### <a name="add-an-outbound-sync-rule"></a>新增輸出同步處理規則：
到目前為止新增只輸入同步處理規則我們完成一半的工作，因為屬性尚未連結到目標目錄。 目標 director 連結屬性，您需要建立輸出規則，這表示來源是 metaverse 以及目標是連接的系統。 若要建立輸出規則，請啟動**同步處理規則編輯器**，變更**方向**來**輸出**然後按一下**新增規則**。 

![預設規則](media/how-to-connect-fix-default-rules/default3c.png)

如所示的輸入規則，您可以使用您自己的命名慣例來**名稱**規則。 選取 **已連線系統**與 Azure AD 租用戶中，選取您要設定屬性值的連線的系統物件。 設定的優先順序介於 0-99。 

![預設規則](media/how-to-connect-fix-default-rules/default3d.png)

保持**範圍設定篩選**空的保持**聯結規則**清空，填入常數、 Direct 或運算式的轉換。 

在此範例中，我們已示範了如何從 Active Directory 到 Azure Active Directory 使用者物件的新屬性。 您可以使用下列步驟來對應中的任何物件來源和目標的任何屬性。  如需詳細資訊，請參閱[建立自訂同步處理規則](how-to-connect-create-custom-sync-rule.md)並[準備將使用者佈建](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="overriding-value-of-existing-attribute"></a>覆寫現有屬性的值
很可能您想要覆寫已對應屬性的值，例如一律要在 Azure AD 中設定屬性的 Null 值，您可以在上一個步驟和 flow 中所述，只要建立只有輸入的規則**AuthoritativeNull**目標屬性的常數值。 請注意，我們使用 AuthoritativeNulll 而不是 Null 在此情況下。 這是因為非 null 值會取代 Null 值，即使它具有較低的優先順序 （較高數字的值在規則中）。 不過，AuthoritativeNull 會被視為 Null，而且將不會取代非 null 值的其他規則。 

### <a name="dont-sync-existing-attribute"></a>不會同步處理現有的屬性
如果您想要同步處理時，排除屬性，您可以使用篩選功能在 Azure AD Connect 所提供的屬性。 啟動**Azure AD Connect**從桌面圖示，然後選取**自訂同步處理選項**。

![預設規則](media/how-to-connect-fix-default-rules/default4.png)

 請確定**Azure AD 應用程式和屬性篩選**已選取，然後按一下**下一步**。

![預設規則](media/how-to-connect-fix-default-rules/default5.png)

取消選取您想要從同步處理中排除的屬性。

![預設規則](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>變更範圍篩選器
Azure AD 同步處理會負責大部分的物件，您可以縮小範圍的物件，並降低它較少的物件，而不需要變更的標準預設同步處理規則，以支援的方式匯出。 如果您想要增加之物件的範圍，您就可以**編輯**現有的規則，將它複製，然後停用原始的規則。 Microsoft 建議您不要增加 Azure AD Connect 所設定的範圍。 增加範圍的物件可讓您更難了解自訂項目，並支援產品的支援小組。

以下是您可以如何降低的同步處理至 Azure AD 的物件範圍。 請注意，如果您的範圍減少**使用者**正在同步處理密碼雜湊同步處理也將會停止已篩選出使用者。 如果物件已同步處理，然後之後減少範圍，篩選出物件會從目標目錄中刪除，請作用於範圍非常謹慎。
以下是支援的方法來減少在同步處理物件的領域。

- [cloudFiltered 屬性](#cloudfiltered-attribute)
- [OU 篩選](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>cloudFiltered 屬性
請注意，這不是可以在 Active Directory 中設定的屬性。 您必須設定此屬性的值，藉由新增新的輸入的規則，如中所述**覆寫現有屬性的值**一節。 然後您可以使用**Transformation**並用**運算式**Metaverse 中設定這個屬性。 以下是範例，您不想要同步所有使用者的部門名稱開頭為不區分大小寫**HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

首先，我們已轉換從來源 (Active Directory) 部門為小寫。 然後使用左側的函式，我們花了前 3 個字元，並比較它與 hrd。 如果相符，然後將值設為 True 的否則為 NULL。 請注意我們設定 NULL 值，以便其他某個規則優先順序較低 （較高數字的值） 可以寫入具有不同的條件。 請驗證同步處理規則中所述的一個物件上執行預覽[驗證同步處理規則](#validate-sync-rule)一節。

![預設規則](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>OU 篩選
您可以建立一或多個 Ou，並將您不想要同步至這些 Ou 的物件移動。 然後設定 OU 篩選 Azure AD Connect 中透過啟動**Azure AD Connect**從桌面圖示並選取上述選項，如下所示。 您也可以設定在 Azure AD connect 的安裝時進行篩選的 OU。 

![預設規則](media/how-to-connect-fix-default-rules/default8.png)

遵循精靈的指示，然後取消選取任何您不想要同步的 Ou。

![預設規則](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>變更聯結條件
Microsoft 建議您，您會使用預設聯結 Azure AD Connect 所設定的條件。 變更預設的聯結條件，就會使其難以了解自訂項目，並支援產品的支援小組。

## <a name="validate-sync-rule"></a>驗證同步處理規則
您可以使用預覽功能，而不需要執行完整同步處理循環，以驗證新加入同步處理規則。 啟動**同步處理服務**UI。

![預設規則](media/how-to-connect-fix-default-rules/default10.png)

按一下**Metaverse 搜尋**，選取範圍的物件，做為**人員**，**加入子句**和提及您的搜尋準則。 按一下 **搜尋**按鈕，然後在物件上按兩下**搜尋結果**請注意，您匯入和同步處理執行樹系上，執行此步驟之前，這是為了確保 Azure AD Connect 中的資料是最新狀態，該物件。

![預設規則](media/how-to-connect-fix-default-rules/default11.png)



選取 **連接器**、 選取的物件相對應的 connector(forest) 中，按一下 **屬性...**.

![預設規則](media/how-to-connect-fix-default-rules/default12.png)

按一下**預覽...**

![預設規則](media/how-to-connect-fix-default-rules/default13a.png)

按一下 **產生預覽**並**匯入屬性流程**的左窗格中。

![預設規則](media/how-to-connect-fix-default-rules/default14.png)
 
這裡您會發現新加入的規則在物件上執行，並已設定為 True 的 cloudFiltered 屬性。

![預設規則](media/how-to-connect-fix-default-rules/default15a.png)
 
如何修改的比較規則，使用預設規則？
您可以為文字檔案，分別匯出這兩個規則。 這些規則將會匯出為 powershell 指令碼檔案。 您可以使用任何檔案比較工具，以查看完成哪一種變更加以比較。 這裡在本例中我使用 windiff 來比較兩個檔案。
 
您可以注意到在使用者修改規則、 msExchMailboxGuid 屬性會變成**運算式**類型，而非**直接**與值**NULL**並**ExecuteOnce**選項。 您可以忽略 Identified 和優先順序的差異。 

![預設規則](media/how-to-connect-fix-default-rules/default17.png)
 
如何修正已修改的預設規則？
若要修正您的規則，以預設設定，您可以刪除已修改的規則和啟用預設規則，如下所示，然後執行**完整同步處理**。 之前這樣做，請採取修正動作，如先前所述，如此您就不會遺失自訂您想要達到 # # 下一步

## <a name="next-steps"></a>後續步驟
- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)

