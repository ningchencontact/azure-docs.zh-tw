---
title: 什麼是 Azure Active Directory 條件式存取中的服務相依性？ | Microsoft Docs
description: 了解條件如何在 Azure Active Directory 條件式存取中使用來觸發原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9aca2e4ea5e107358ff72e83562057830ece2cc
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509344"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>什麼是 Azure Active Directory 條件式存取中的服務相依性？ 

使用條件式存取原則，您可以指定網站和服務的存取需求。 比方說，您的存取需求可以包含需要 multi-factor authentication (MFA) 或[受管理的裝置](require-managed-devices.md)。 

當您直接存取網站或服務時，相關原則的影響是通常很容易評估。 例如，如果您有一個原則，要求使用 MFA 進行 SharePoint Online 設定時，MFA 會強制執行的每個登入 SharePoint 入口網站中。 不過，它不一定簡單來評估原則的影響，因為有其他雲端應用程式的相依性的雲端應用程式。 例如，Microsoft Teams 可在 SharePoint Online 資源的存取權。 因此，當您存取 Microsoft Teams 中我們目前案例時，您也會受限於 SharePoint MFA 原則。   

## <a name="policy-enforcement"></a>強制執行原則 

如果您有服務相依性設定時，可能會使用早期繫結或晚期繫結的強制套用原則。 

- **早期繫結原則強制執行**表示使用者必須滿足才能存取呼叫端的應用程式的相依服務的原則。 比方說，使用者必須登入 MS Teams 之前符合 SharePoint 原則。 
- **晚期繫結原則強制執行**後面使用者登入呼叫的應用程式。 呼叫應用程式要求，下游服務的權杖時，強制執行會延後到。 範例包括存取規劃工具和 Office.com 存取 SharePoint 的 MS Teams。 

下圖說明 MS Teams 服務相依性。 實心箭號表示早期繫結強制虛線的箭號，Planner 表示晚期繫結強制執行。 

![MS Teams 服務相依性](./media/service-dependencies/01.png)

最佳做法，您應該設定跨相關應用程式和服務，請盡可能的常見的原則。 具有一致的安全性狀態可以提供最佳使用者體驗。 比方說，在 Exchange Online、 SharePoint Online、 Microsoft Teams 及 Skype 中將通用的原則設定企業時，大幅降低非預期的提示，可能會發生不同的原則套用至下游服務的。 

下表列出的其他服務的相依性，必須滿足用戶端應用程式的位置  

| 用戶端應用程式         | 下游服務                          | 強制執行 |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure 管理 （入口網站和 API） | 早期繫結 |
| Microsoft 教室 | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結  |
| Microsoft Teams     | Exchange                                    | 早期繫結 |
|                     | MS 規劃工具                                  | 晚期繫結  |
|                     | SharePoint                                  | 早期繫結 |
|                     | 商務用 Skype Online                   | 早期繫結 |
| Office 入口網站       | Exchange                                    | 晚期繫結  |
|                     | SharePoint                                  | 晚期繫結  |
| Outlook 的群組      | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| PowerApps           | Microsoft Azure 管理 （入口網站和 API） | 早期繫結 |
|                     | Windows Azure Active Directory              | 早期繫結 |
| 隨附此逐步解說的專案             | Dynamics CRM                                | 早期繫結 |
| 商務用 Skype  | Exchange                                    | 早期繫結 |
| Visual Studio       | Microsoft Azure 管理 （入口網站和 API） | 早期繫結 |

## <a name="next-steps"></a>後續步驟

若要了解如何在環境中實作條件式存取，請參閱[計劃在 Azure Active Directory 中的條件式存取部署](plan-conditional-access.md)。
