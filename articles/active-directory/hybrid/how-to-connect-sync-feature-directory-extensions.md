---
title: Azure AD Connect 同步：目錄擴充 | Microsoft Docs
description: 本主題說明 Azure AD Connect 中的目錄擴充功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff0fd4d01eab739b79685c1de67cb8fe28873961
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167196"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步：目錄擴充
您可以使用目錄擴充功能，從內部部署 Active Directory 利用自己的屬性擴充 Azure Active Directory (Azure AD) 中的結構描述。 此功能可讓您建置 LOB 應用程式，方法是取用您在內部部署中持續進行管理的屬性。 透過 [Azure AD 圖形 API 目錄擴充功能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或 [Microsoft Graph](https://developer.microsoft.com/graph/) 即可取用這些屬性。 若要查看可用的屬性，您可以分別使用 [Azure AD Graph 總管](https://graphexplorer.azurewebsites.net/)和 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。

目前沒有任何 Office 365 工作負載取用這些屬性。

您可以在安裝精靈的自訂設定路徑中，設定您想要同步處理的其他屬性。

>[!NOTE]
>[可用屬性] 方塊區分大小寫。

![結構描述擴充功能精靈](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

 安裝會顯示下列屬性，這些都是有效的候選項目：

* 使用者和群組物件類型
* 單一值屬性：字串、布林值、整數、二進位
* 多重值屬性：字串、二進位


>[!NOTE]
> 儘管 Azure AD Connect 支援將多重值的 Active Directory 屬性同步處理為 Azure AD 作為多重值的目錄擴充，但是目前沒有辦法可以擷取/取用在多重值目錄擴充屬性中上傳的資料。

屬性清單是從 Azure AD Connect 安裝期間建立的結構描述快取讀取。 如果您已使用其他屬性擴充 Active Directory 結構描述，就必須[重新整理結構描述](how-to-connect-installation-wizard.md#refresh-directory-schema)，才可看見這些新屬性。

Azure AD 中的物件最多可有 100 個目錄擴充功能的屬性。 長度上限是 250 個字元。 如果屬性值較長，同步處理引擎就會加以截斷。

在 Azure AD Connect 安裝期間，會註冊可提供這些屬性的應用程式。 您可以在 Azure 入口網站中看到這個應用程式。

![結構描述擴充功能應用程式](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

屬性的前面會加上副檔名 \_{AppClientId}\_。 Azure AD 租用戶中的所有屬性會有相同的 AppClientId 值。

現在可透過 Azure AD 圖形 API 提供這些屬性。 您可以使用 [Azure AD Graph 總管](https://graphexplorer.azurewebsites.net/)進行查詢。

![Azure AD Graph 總管](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

或者，您可以使用 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer#)，透過 Microsoft 圖形 API 查詢屬性。

>[!NOTE]
> 您必須要求要傳回的屬性。 明確地選取屬性，像是： https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division。 
>
> 如需詳細資訊，請參閱 [Microsoft Graph：使用查詢參數](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter) \(英文\)。

## <a name="next-steps"></a>後續步驟
深入了解 [Azure AD Connect 同步](how-to-connect-sync-whatis.md) 組態。

深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
