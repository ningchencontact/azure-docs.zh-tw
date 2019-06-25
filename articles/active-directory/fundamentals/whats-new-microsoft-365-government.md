---
title: 什麼是 Azure Active Directory，Microsoft 365 Government Azure Active Directory 中的新功能 |Microsoft Docs
description: 深入了解 Azure Active directory (Azure AD) 中 Microsoft 365 Government 雲端執行個體，可能會影響您的某些變更。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258890"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>什麼是 Azure Active Directory，Microsoft 365 Government 中的新功能

Microsoft 365 Government 雲端執行個體，也就是適用於使用下列服務的客戶中，我們已到 Azure Active Directory (Azure AD) 進行一些變更：

- Microsoft Azure Government

- Microsoft 365 Government – GCC 高

- Microsoft 365 Government – DoD

本文不適用於 Microsoft 365 Government – GCC 客戶。

## <a name="changes-to-the-initial-domain-name"></a>初始網域名稱的變更

在貴組織的初始註冊以取得 Microsoft 365 政府版的線上服務，您必須選擇您的組織網域名稱， `<your-domain-name>.onmicrosoft.com`。 如果您已經有.com 尾碼的網域名稱，不會變更。

不過，如果您要註冊新的 Microsoft 365 Government 服務，您需要選擇網域名稱使用`.us`後置詞。 因此，它會`<your-domain-name>.onmicrosoft.us`。

>[!Note]
>此變更不適用於任何雲端服務提供者 (Csp) 所管理的客戶。

## <a name="changes-to-portal-access"></a>若要存取入口網站的變更

中所示，我們已針對 Microsoft Azure Government，Microsoft 365 Government – GCC High 與 Microsoft 365 Government – DoD、 更新入口網站的端點[端點對應表](#endpoint-mapping)。

先前的客戶無法登入使用全球 Azure (portal.azure.com) 和 Office 365 (portal.office.com) 入口網站。 此更新中，客戶現在必須登入使用特定的 Microsoft Azure Government，Microsoft 365 Government-GCC High 與 Microsoft 365 Government DoD 入口網站。

## <a name="endpoint-mapping"></a>端點對應

下表顯示的端點，讓所有客戶：

| Name | 端點詳細資料 |
|------|------------------|
| 入口網站 |Microsoft Azure 政府服務： https://portal.azure.us<p>Microsoft 365 Government – GCC 高： https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Azure Active Directory 授權端點 | https://login.microsoftonline.us |
| Azure Active Directory 圖形 API | https://graph.windows.net |
| Microsoft Graph API 適用於 Microsoft 365 Government-GCC 高 | https://graph.microsoft.us |
| 適用於 Microsoft 365 Government-DoD 的 Microsoft Graph API | https://dod-graph.microsoft.us |
| Azure Government 服務端點 | 如需詳細資訊，請參閱[Azure Government 開發人員指南](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government-GCC High 的端點 | 如需詳細資訊，請參閱[Office 365 美國Government GCC High 的端點](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government-DoD | 如需詳細資訊，請參閱[Office 365 美國Government DoD 端點](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱這些文章：

- [什麼是 Azure Government？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure Government AAD 授權單位端點更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [美國政府雲端中的 Microsoft Graph 端點](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High 和 DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)