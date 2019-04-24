---
title: 身分識別同步處理和重複屬性恢復功能 | Microsoft Docs
description: 在目錄同步處理期間如何使用 Azure AD Connect 來處理具有 UPN 或 ProxyAddress 衝突之物件的新行為。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65af5a5ea0629b617c4e736d8c110cbb9aa540c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60348760"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>标识同步和重复属性复原
「重複屬性恢復功能」是 Azure Active Directory 中的一項功能，可在執行 Microsoft 的其中一個同步處理工具時，用來消除 **UserPrincipalName** 和 **ProxyAddress** 衝突所造成的不便。

在指定之 Azure Active Directory 租用戶的所有「使用者」、「群組」或「連絡人」物件中，這兩個屬性通常必須是唯一的。

> [!NOTE]
> 只有使用者可以擁有 UPN。
> 
> 

這項功能賦予了同步管線雲端部分的新行為，因此對於任何 Microsoft 同步處理產品 (包括 Azure AD Connect、DirSync 和 MIM + 連接器) 而言，此功能不限於特定用戶端並與用戶端相關。 在本文件中，通用詞彙「同步用戶端」將用來代表上述任何一項產品。

## <a name="current-behavior"></a>目前的行為
如果嘗試佈建的新物件具有違反此唯一性條件約束的 UPN 或 ProxyAddress 值，則 Azure Active Directory 不會建立該物件。 同样地，如果以非唯一的 UPN 或 ProxyAddress 更新对象，则更新会失败。 同步用戶端會在每個匯出週期重試佈建嘗試或更新，在衝突解決前會繼續失敗。 每次尝试时都会生成错误报告电子邮件，并由同步客户端记录一个错误。

## <a name="behavior-with-duplicate-attribute-resiliency"></a>重複屬性恢復功能的行為
Azure Active Directory 並不是完全無法佈建或更新具有重複屬性的物件，而是會「隔離」違反唯一性條件約束的重複屬性。 如果佈建時需要此屬性 (例如 UserPrincipalName)，則服務會指派預留位置值。 這些暫存值的格式為  
「***\<OriginalPrefix > +\<4DigitNumber >\@\<InitialTenantDomain >。 onmicrosoft.com***"。  
如果不需要此屬性 (例如 **ProxyAddress**)，Azure Active Directory 就會隔離衝突屬性，然後繼續建立或更新物件。

隔離屬性時，衝突相關資訊會以舊版行為中使用的相同錯誤報告電子郵件傳送。 不過，此資訊只會出現在錯誤報告中一次，發生隔離時，將不會繼續記錄在未來的電子郵件中。 此外，由于此对象已成功导出，因此同步客户端不会记录错误，并且不会在后续的同步周期中重试创建/更新操作。

為了支援此行為，[使用者]、[群組] 和 [連絡人] 物件類別已加入新屬性︰  
**DirSyncProvisioningErrors**

這是多重值的屬性，用來儲存正常新增時會違反唯一性條件約束的衝突屬性。 Azure Active Directory 中已啟用背景計時器工作，該工作會每小時執行以尋找已解決的重複屬性衝突，然後從隔離區中自動移除上述屬性。

### <a name="enabling-duplicate-attribute-resiliency"></a>启用重复属性复原
「重複屬性恢復功能」將會是所有 Azure Active Directory 租用戶的新預設行為。 針對所有在 2016 年 8 月 22 日或之後第一次啟用同步處理的租用戶，預設都會開啟這項功能。 在此日期之前啟用同步功能的租用戶則會分批啟用此功能。 這項功能推出將從 2016 年 9 月開始，每個租用戶的技術通知連絡人將會收到電子郵件通知，其中會指出啟用功能的特定日期。

> [!NOTE]
> 一旦開啟「重複屬性恢復功能」，即無法予以停用。

如果要查看是否为租户启用了此功能，可以下载最新版 Azure Active Directory PowerShell 模块，并运行以下命令：

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> 在為租用戶開啟該功能之前，您無法再使用 Set-MsolDirSyncFeature Cmdlet 來主動啟用重複屬性復原功能。 若要測試該功能，您需要建立新的 Azure Active Directory 租用戶。

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>識別具有 DirSyncProvisioningErrors 的物件
目前有兩種方法可以找出有這些錯誤，因為重複屬性衝突，Azure Active Directory PowerShell 的物件和[Microsoft 365 系統管理中心](https://admin.microsoft.com)。 未來計劃擴充其他以入口網站為基礎的報告。

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
對於本主題中的 PowerShell Cmdlet，下列項目為真︰

* 下列所有 Cmdlet 都會區分大小寫。
* 一律包含 **–ErrorCategory PropertyConflict** 。 目前沒有其他類型的 **ErrorCategory**，但可能會在未來加以擴充。

首先，執行 **Connect-MsolService** 並輸入租用戶系統管理員的認證。

接著，使用下列 Cmdlet 和運算子，以不同的方式檢視錯誤︰

1. [檢視全部](#see-all)
2. [依屬性類型](#by-property-type)
3. [依衝突的值](#by-conflicting-value)
4. [使用字串搜尋](#using-a-string-search)
5. 排序
6. [以有限的數量或全部](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>查看全部
连接后，若要查看租户中属性预配错误的常规列表，请运行：

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

随后会生成如下所示的结果：  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>依屬性類型
若要依屬性類型查看錯誤，請搭配 **UserPrincipalName** 或 **ProxyAddresses** 引數新增 **-PropertyName** 旗標︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

或

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>依衝突的值
若要查看與特定屬性相關的錯誤，請新增 **-PropertyValue** 旗標 (新增此旗標時，必須一併使用 **-PropertyName**)：

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>使用字串搜尋
若要進行廣泛的字串搜尋，請使用 **-SearchString** 旗標。 此旗標可以與上述所有旗標獨立使用，但一律為必要項目的 **-ErrorCategory PropertyConflict**除外︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>以有限的數量或全部
1. **MaxResults \<Int>** 可用于将查询限制为特定数目的值。
2. **All** 可用於確保在有大量錯誤的情況下擷取所有的結果。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Microsoft 365 管理中心
可以在 Microsoft 365 管理中心查看目录同步错误。 Microsoft 365 管理中心的报告只显示存在这些错误的 **User** 对象。 並不會顯示「群組」和「連絡人」之間的衝突相關資訊。

![作用中使用者](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "作用中使用者")

有关如何在 Microsoft 365 管理中心查看目录同步错误的说明，请参阅[识别 Office 365 中的目录同步错误](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)。

### <a name="identity-synchronization-error-report"></a>身分識別同步處理錯誤報告
當利用這個新行為處理具有重複屬性衝突的物件時，通知會包含在標準身分識別同步處理錯誤報告電子郵件中，而該電子郵件回傳送給租用戶的技術通知連絡人。 不過，此行為有一項重大變更。 在过去，有关重复属性冲突的信息包含在每个后续错误报告中，直到解决冲突为止。 利用這個新行為，給定衝突的錯誤通知只會出現一次 - 在衝突的屬性遭到隔離時。

ProxyAddress 冲突的电子邮件通知示例如下所示：  
    ![作用中使用者](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "作用中使用者")  

## <a name="resolving-conflicts"></a>解決衝突
這些錯誤的疑難排解策略和解決技巧不得與過去處理重複屬性錯誤的方式不同。 唯一的差別在於計時器工作將會掃掠服務端上的租用戶，以在衝突解決後，自動將有問題的屬性新增至適當的物件。

下列文章概述各種疑難排解和解決策略︰[重複或無效的屬性使 Office 365 無法進行目錄同步處理](https://support.microsoft.com/kb/2647098)。

## <a name="known-issues"></a>已知問題
沒有任何已知問題會導致資料遺失或服務降級。 其中有幾個是美觀的問題，其他問題會導致擲回標準「恢復前」重複屬性錯誤，而不是隔離衝突屬性，而別的問題會導致特定錯誤需要額外的手動修復。

**核心行為︰**

1. 具有特定屬性組態的物件會繼續收到匯出錯誤，而不是將重複屬性隔離。  
   例如︰
   
    a. 在 AD 中创建一个新用户，其 UPN 为 **Joe\@contoso.com**，ProxyAddress 为 **smtp:Joe\@contoso.com**
   
    b. 此对象的属性与现有 Group 发生冲突，其中 ProxyAddress 为 **SMTP:Joe\@contoso.com**。
   
    c. 匯出時，會擲回「ProxyAddress 衝突」 錯誤，而不是將衝突屬性隔離。 此作業會在每個後續的同步處理週期中重試，就如同在啟用恢復功能之前一樣。
2. 如果在內部部署上建立兩個具有相同 SMTP 位址的群組，則會在第一次嘗試時佈建失敗並發生標準的重複 **ProxyAddress** 錯誤。 不過，重複值會在下一個同步處理週期時被適當隔離。

**Office 入口網站報告**：

1. UPN 衝突集中兩個物件的詳細錯誤訊息是相同的。 這表示它們都已變更 / 隔離 UPN，當事實上只有其中一個變更了資料。
2. UPN 衝突的詳細錯誤訊息會對已變更/隔離其 UPN 的使用者，顯示錯誤的 displayName。 例如︰
   
    a. **用户 A** 首先使用 **UPN = User\@contoso.com** 同步。
   
    b. 然后，尝试使用 **UPN = User\@contoso.com** 同步**用户 B**。
   
    c. **User B** UPN 會變成**User1234\@contoso.onmicrosoft.com**並**使用者\@contoso.com**會新增至**DirSyncProvisioningErrors**.
   
    d. **用户 B** 的错误消息应指出**用户 A** 已有用作 UPN 的 **User\@contoso.com**，但却显示**用户 B** 自己的 displayName。

**身分識別同步處理錯誤報告**：

“关于如何解决此问题的步骤”链接不正确：  
    ![作用中使用者](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "作用中使用者")  

它應該指向 [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)。

## <a name="see-also"></a>請參閱
* [Azure AD Connect 同步處理](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)
* [在 Office 365 中識別目錄同步處理錯誤](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

