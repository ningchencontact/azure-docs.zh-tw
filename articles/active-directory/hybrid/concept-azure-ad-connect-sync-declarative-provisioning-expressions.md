---
title: Azure AD Connect：宣告式佈建運算式 | Microsoft Docs
description: 說明宣告式佈建運算式。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/18/2017
ms.date: 11/08/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245511"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect 同步：了解宣告式佈建運算式
Azure AD Connect 同步處理是以 Forefront Identity Manager 2010 中最先引進的宣告式佈建為基礎。 它可讓您實作完整的身分識別整合商務邏輯，而不需要撰寫已編譯的程式碼。

宣告式佈建最主要的部分是在屬性流程中使用運算式語言。 使用的語言是 Microsoft® Visual Basic® for Applications (VBA) 的子集。 此語言用於 Microsoft Office，而且擁有 VBScript 經驗的使用者也可以辨識它。 宣告式佈建運算式語言只會使用函式，而不是結構化語言。 沒有任何方法或陳述式。 函式會改為巢狀函式來表示程式流程。

如需詳細資訊，請參閱 [歡迎使用適用於 Office 2013 的 Visual Basic for Applications 語言參考](https://msdn.microsoft.com/library/gg264383.aspx)。

屬性是強型別。 函式只會接受正確類型的屬性。 它也區分大小寫。 函式名稱和屬性名稱兩者都必須有正確的大小寫，否則會擲回錯誤。

## <a name="language-definitions-and-identifiers"></a>語言定義和識別項
- 函式名稱後面接著以括弧括住的引數：FunctionName(argument 1, argument N)。
- 屬性以方括弧識別：[attributeName]
- 參數以百分比符號識別：%ParameterName%
- 字串常數以引號括住：例如 "Contoso" (注意：必須使用一般引號 ""，而非智慧引號 “”)
- 數值不加引號，而且必須是十進位。 十六進位值前面會加上 &H。 例如，98052、&HFF
- 布林值以下列常數表示：True、False。
- 內建常數和常值只以其名稱表示：NULL、CRLF、IgnoreThisFlow

### <a name="functions"></a>Functions
宣告式佈建會使用許多函式，來啟用轉換屬性值的可能性。 這些函式可以是巢狀的，因此，來自某一個函式的結果會傳遞到另一個函式。

`Function1(Function2(Function3()))`

如需函式的完整清單，請參閱 [函式參考](reference-connect-sync-functions-reference.md)。

### <a name="parameters"></a>參數
參數由連接器或由系統管理員使用 PowerShell 定義。 參數通常會包含隨系統而不同的值，例如使用者所在的網域名稱。 這些參數可用於屬性流程中。

Active Directory 連接器對於輸入同步處理規則提供下列參數：

| 参数名称 | 註解 |
| --- | --- |
| Domain.Netbios |目前正在匯入之網域的 NetBIOS 格式，例如 FABRIKAMSALES |
| Domain.FQDN |目前正在匯入之網域的 FQDN 格式，例如 sales.fabrikam.com |
| Domain.LDAP |目前正在匯入之網域的 LDAP 格式，例如 DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios |目前正在匯入之樹系名稱的 Netbios 格式，例如 FABRIKAMCORP |
| Forest.FQDN |目前正在匯入之樹系名稱的 FQDN 格式，例如 fabrikam.com |
| Forest.LDAP |目前正在匯入之樹系名稱的 LDAP 格式，例如 DC=fabrikam,DC=com |

系統會提供下列參數，用來取得目前正在執行之連接器的識別碼：  
`Connector.ID`

以下範例會以使用者所在網域的 netbios 名稱填入 Metaverse 屬性網域：  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>運算子
可以使用下列運算子：

- **比較**：<、<=、<>、=、>、>=
- **數學**：+、-、\*、-
- **字串**：& (串連)
- **邏輯**：&& (且)、|| (或)
- **評估順序**：( )

運算子會由左至右進行評估，且具有相同的評估優先順序。 也就是，不會在 - (減號) 之前評估 \* (乘數)。 2\*(5+3) 與 2\*5+3 不同。 如果从左到右的计算顺序不适当，则使用括号 () 来更改计算顺序。

## <a name="multi-valued-attributes"></a>多重值屬性
函式可以在單一值和多重值屬性上操作。 對於多重值屬性，函式會在每個值上進行操作並將相同的函式套用到每個值。

例如，  
`Trim([proxyAddresses])` 对 proxyAddress 属性中的每个值执行 Trim。  
`Word([proxyAddresses],1,"@") & "@contoso.com"` 对于包含 @-sign 的每个值，将域替换为 @contoso.com。  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` 查找 SIP 地址并从值中删除该地址。

## <a name="next-steps"></a>後續步驟
- 在[了解声明性预配](concept-azure-ad-connect-sync-declarative-provisioning.md)中阅读有关配置模型的详细信息。
- 在[了解默认配置](concept-azure-ad-connect-sync-default-configuration.md)中了解如何现成使用声明性设置。
- 如需了解如何使用宣告式佈建進行實際變更，請參閱 [如何變更預設組態](how-to-connect-sync-change-the-configuration.md)。

**概觀主題**

- [Azure AD Connect 同步：了解並自訂同步處理](how-to-connect-sync-whatis.md)
- [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)

**參考主題**

- [Azure AD Connect 同步：函式參考](reference-connect-sync-functions-reference.md)


