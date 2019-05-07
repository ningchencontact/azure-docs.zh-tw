---
title: 如何修复已修改的默认规则 - Azure AD Connect | Microsoft Docs
description: 了解如何修复 Azure AD Connect 随附的经过修改的默认规则。
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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067641"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>在 Azure AD Connect 中修复已修改的默认规则

Azure Active Directory (Azure AD) Connect 會使用預設規則的同步處理。  不幸的是，這些規則都不適用於所有的組織。 根據您的需求，您可能需要加以修改。 這篇文章討論最常見的自訂項目，兩個範例，並說明完成這些自訂的正確方式。

>[!NOTE] 
> 不支援修改現有的預設規則，以達到所需的自訂。 如果您這樣做，它會防止在未來更新為最新版本的這些規則會釋放。 您無法取得您需要 bug 修正或新功能。 本文件說明如何達成相同的結果，而不需修改現有的預設規則。 

## <a name="how-to-identify-modified-default-rules"></a>如何找出修改過的預設規則
從 Azure AD connect 的 1.3.7.0 版開始，很容易識別修改過的預設規則。 移至**桌面上的應用程式**，然後選取**同步處理規則編輯器**。

![Azure AD Connect，反白顯示的同步處理規則編輯器](media/how-to-connect-fix-default-rules/default1.png)

在編輯器中，會顯示警告圖示名稱前面的任何修改過的預設規則。

![警告圖示](media/how-to-connect-fix-default-rules/default2.png)

 已停用規則具有相同名稱旁邊也會出現 （這是標準的預設規則）。

![同步處理規則編輯器，顯示標準的預設規則，並已修改的預設規則](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常见自定义操作
下面是对默认规则执行的常见自定义操作：

- 變更屬性流程
- 變更範圍篩選器
- 變更聯結條件

之前，您要變更任何規則：

- 禁用同步计划程序。 默认情况下，计划程序每隔 30 分钟运行一次。 請確定未啟動，而您要進行變更，並疑難排解您的新規則。 若要暫時停用排程器，啟動 PowerShell，然後執行`Set-ADSyncScheduler -SyncCycleEnabled $false`。
 ![若要停用同步排程器的 PowerShell 命令](media/how-to-connect-fix-default-rules/default3.png)

- 範圍篩選器中的變更可能會導致刪除目標目錄中的物件。 請小心在領域的物件進行任何變更之前。 我們建議您到預備伺服器進行變更，再使用中伺服器上進行變更。
- 單一物件上執行預覽，如中所述[驗證同步處理規則](#validate-sync-rule)區段中，新增任何新的規則之後。
- 執行完整同步處理之後加入新規則，或修改任何自訂的同步處理規則。 此同步處理會將新的規則套用到所有物件。

## <a name="change-attribute-flow"></a>變更屬性流程
有三種不同的案例，變更屬性流程：
- 加入新屬性。
- 覆寫現有屬性的值。
- 選擇不進行同步處理現有的屬性。

您可以執行這些而不需要變更標準的預設規則。

### <a name="add-a-new-attribute"></a>新增屬性
如果您發現從來源目錄，不會將屬性流動的目標目錄，請使用[Azure AD Connect 同步：目錄擴充](how-to-connect-sync-feature-directory-extensions.md)要修正此問題。

如果延伸模組不適合您，請嘗試加入兩個新的同步處理規則，下列各節所述。


#### <a name="add-an-inbound-sync-rule"></a>新增輸入同步處理規則
輸入同步處理規則表示屬性的來源是連接器空間，而且目標會在 metaverse。 比方說，有新的屬性流程從內部部署 Active Directory 以 Azure Active Directory 中，建立新的輸入同步處理規則。 啟動**同步處理規則編輯器**，選取**Inbound**作為方向，然後選取**新增規則**。 

 !同步處理規則 Editor](media/how-to-connect-fix-default-rules/default3a.png)

請遵循您自己的命名慣例來命名規則。 在這裡，我們使用**自訂時間 In from AD-使用者**。 這表示規則是一個自訂的規則，而且是從 Active Directory 連接器空間到 metaverse 的輸入的規則。   

 ![建立輸入同步處理規則](media/how-to-connect-fix-default-rules/default3b.png)

提供您自己的規則，描述，以便未來維護的規則很簡單。 例如，描述可以根據規則的目標為何，以及為何需要它。

讓您選取項目**已連線系統**，**連線系統物件類型**，並**Metaverse 物件類型**欄位。

指定從 0 到 99 的優先順序值 (較低數字，最高優先順序)。 針對**標記**，**啟用密碼同步處理**，並**已停用**欄位中，使用預設的選取項目。

保持**範圍設定篩選**空白。 這表示，此規則會套用至所有物件加入 Active Directory 已連線系統和 metaverse 之間。

保持**聯結規則**空白。 這表示此規則會使用標準的預設規則中所定義的聯結條件。 這是不能停用或刪除標準的預設規則的另一個原因。 如果沒有任何聯結條件，將不會流向屬性。 

新增適當的轉換屬性。 您可以指派要做為常數的常數，值流向您的目標屬性。 您可以使用的來源或目標屬性之間的直接對應。 或者，您可以使用運算式的屬性。 下面是可以使用的各种[表达式函数](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)。

#### <a name="add-an-outbound-sync-rule"></a>新增輸出同步處理規則
若要連結到目標目錄的屬性，您需要建立輸出規則。 這表示來源是 metaverse，而且目標是連接的系統。 若要建立輸出規則，請啟動**同步處理規則編輯器**，變更**方向**來**輸出**，然後選取**新增規則**. 

![同步處理規則編輯器](media/how-to-connect-fix-default-rules/default3c.png)

輸入的規則，您可以使用您自己的命名慣例命名規則。 選取 **已連線系統**作為 Azure AD 租用戶中，選取連接的系統中您想要的物件設定屬性值。 設定 0 到 99 間的優先順序。 

![建立輸出同步處理規則](media/how-to-connect-fix-default-rules/default3d.png)

保持**範圍設定篩選**並**聯結規則**空白。 填寫常數、 直接、 或運算式的轉換。 

您現在知道如何從 Active Directory 中對 Azure Active Directory 的使用者物件流程的新屬性。 可以使用这些步骤将任意对象中的任意属性映射到源和目标。 如需詳細資訊，請參閱 <<c0> [ 建立自訂同步處理規則](how-to-connect-create-custom-sync-rule.md)並[準備將使用者佈建](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="override-the-value-of-an-existing-attribute"></a>覆寫現有屬性的值
您可能想要覆寫已對應屬性的值。 比方說，如果您一定想要在 Azure AD 中設定屬性的 null 值，只要建立只有輸入的規則。 請常數值， `AuthoritativeNull`，目標屬性的流程。 

>[!NOTE] 
> 使用`AuthoritativeNull`而不是`Null`在此情況下。 這是因為非 null 值取代 null 值，即使它具有較低的優先順序 （較高數字的值在規則中）。 `AuthoritativeNull`相反地，不會取代為非 null 值與其他規則。 

### <a name="dont-sync-existing-attribute"></a>不同步现有属性
如果您想要同步處理時，排除屬性，請使用篩選功能在 Azure AD Connect 所提供的屬性。 啟動**Azure AD Connect**從桌面圖示，然後選取**自訂同步處理選項**。

![Azure AD Connect 其他工作選項](media/how-to-connect-fix-default-rules/default4.png)

 請確定**Azure AD 應用程式和屬性篩選**已選取，然後選取**下一步**。

![Azure AD Connect 的選用功能](media/how-to-connect-fix-default-rules/default5.png)

清除您想要從同步處理中排除的屬性。

![Azure AD Connect 屬性](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>變更範圍篩選器
Azure AD 同步處理會負責大部分的物件。 您可以縮小範圍的物件，並減少要匯出，而不需要變更的標準預設同步處理規則的物件數目。 

您可以使用其中一種下列方法來減少要同步的物件的範圍：

- cloudFiltered 属性
- 組織單位篩選

如果您要同步的使用者的範圍減少，，密碼雜湊同步處理也會停止已篩選出使用者。 如果物件已同步處理之後您縮小範圍,，篩選出刪除的物件會從目標目錄。 基於這個理由，請確定您範圍非常謹慎。

>[!IMPORTANT] 
> 不建議增加的 Azure AD Connect 所設定的物件範圍。 這樣做會很困難，Microsoft 支援小組，以了解自訂項目。 如果您必須增加之物件的範圍，編輯現有的規則、 複製它，並停用原始的規則。 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 属性
您無法在 Active Directory 中設定這個屬性。 藉由新增新的輸入的規則設定此屬性的值。 然後您可以使用**Transformation**並**運算式**metaverse 中設定這個屬性。 下列範例示範您不想要同步處理部門名稱開頭的所有使用者**HRD** （不區分大小寫）：

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

我們先轉換該部門從來源 (Active Directory) 為小寫。 然後，使用`Left`函式中，我們花了只有前三個字元，並比較它與`hrd`。 如果相符的話，要將值設為`True`，否則為`NULL`。 在將值設定為 null，優先順序較低 （較高數字的值） 的一些其他規則可以寫入它與不同的條件。 若要驗證同步處理規則中所述的一個物件上執行的預覽[驗證同步處理規則](#validate-sync-rule)一節。

![建立輸入同步處理規則選項](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>組織單位篩選
您可以建立一或多個組織單位 (Ou)，並將您不想要同步至這些 Ou 的物件移動。 然後，設定 Azure AD Connect 中篩選的 OU。 啟動**Azure AD Connect**從桌面圖示，選取下列選項。 也可以在安装 Azure AD Connect 时配置 OU 筛选。 

![Azure AD Connect 其他工作](media/how-to-connect-fix-default-rules/default8.png)

遵循精靈的指示，並清除您不想要同步的 Ou。

![Azure AD 連線的網域和 OU 篩選選項](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>變更聯結條件
使用 Azure AD Connect 所設定的預設聯結條件。 變更預設的聯結條件就難了解自訂項目，並支援產品的 Microsoft 支援服務。

## <a name="validate-sync-rule"></a>验证同步规则
您可以使用預覽功能，而不需要執行完整同步處理循環，以驗證新加入同步處理規則。 在 Azure AD Connect，請選取**同步處理服務**。

![Azure AD Connect，反白顯示的同步處理服務](media/how-to-connect-fix-default-rules/default10.png)

選取  **Metaverse 搜尋**。 選取的範圍物件**person**，選取**加入子句**，和提及您的搜尋準則。 接下來，選取**搜尋**，然後按兩下搜尋結果中的物件。 請確定您在 Azure AD Connect 中的資料匯入和同步處理執行樹系上，然後再執行此步驟中會是最新的物件，該物件。

![同步處理服務管理員](media/how-to-connect-fix-default-rules/default11.png)

在  **Metaverse 物件屬性**，選取**連接器**，選取對應的連接器 （樹系） 中的物件，然後選取**屬性...**.

![Metaverse 物件屬性](media/how-to-connect-fix-default-rules/default12.png)

選取**預覽...**

![連接器空間物件屬性](media/how-to-connect-fix-default-rules/default13a.png)

在 [預覽] 視窗中，選取**產生預覽**並**匯入屬性流程**的左窗格中。

![預覽](media/how-to-connect-fix-default-rules/default14.png)
 
請注意新加入的規則，在物件上執行，而且已設定`cloudFiltered`屬性設為 true。

![預覽](media/how-to-connect-fix-default-rules/default15a.png)
 
若要比較的預設規則使用修改過的規則，將匯出這兩個規則分別為文字檔案。 這些規則會匯出為 PowerShell 指令碼檔案。 您可以使用任何檔案比較工具 (例如，windiff) 才能看到這些變更來比較它們。 
 
請注意，在修改過的規則`msExchMailboxGuid`屬性變更為**運算式**型別，而不是**直接**。 此外，將值變更為**NULL**並**ExecuteOnce**選項。 可以忽略 Identified（已识别）和 Precedence（优先顺序）的差异。 

![windiff 工具輸出](media/how-to-connect-fix-default-rules/default17.png)
 
若要修正您的規則，若要變更這些預設設定，刪除已修改的規則，並啟用預設規則。 請確定您不會遺失您嘗試要達到的自訂。 當您準備好時，執行**完整同步處理**。

## <a name="next-steps"></a>後續步驟
- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)



