---
title: Dynamics 365 for Customer Engagement 供應項目的必要條件 - Azure Marketplace | Microsoft Docs
description: 在 Azure Marketplace 上發佈 Azure 供應項目的先決條件。
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 4b4859c41e7a3903de68b62e8587f1c85805a782
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081848"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 for Customer Engagement 必要條件

本文說明在 AppSource Marketplace 上發佈 Dynamics 365 for Customer Engagement 應用程式供應項目的技術性和商業上的必要條件。


## <a name="technical-requirements"></a>技術需求

您的 Dynamics 365 for Customer Engagement 應用程式必須符合 [Microsoft AppSource 應用程式檢閱指導方針](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf)，其中包括下列需求：


|              需求             |        說明           |
|            ---------------           |      ---------------         |
| Azure Active Directory 整合   | 您的應用程式必須允許 Azure Active Directory 同盟單一登入 (AAD 同盟 SSO) 並同意啟用。 如需詳細資訊，請參閱[如何讓 AppSource 取得 Azure Active Directory 認證](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified)。 |
| 與 Microsoft Cloud 服務整合 (選擇性) | 需使用此功能時，您的應用程式應與 Microsoft Power BI、Microsoft Flow 或 Microsoft Azure 服務 (如機器學習或認知服務) 等其他 Microsoft Cloud 服務整合。 |
| 著重於企業營運            |  您的應用程式必須著重於定義完善的商務程序或問題、以企業客戶作為主要目標，並且讓使用者能夠以其工作認證 (使用者名稱和密碼) 登入。  |
| 免費試用期與試用版體驗 |  客戶必須能夠在限定時間內免費使用您的應用程式：包括「立即取得」免費應用程式、指定期間內的「免費試用」、「試用產品」體驗，或「與我連絡」要求選項。  |
| 不需設定/低度設定                 | 應用程式的安裝與設定必須簡單快速，且無須開發或自訂。  |
| 客戶支援                     | 應用程式的支援必須包含支援連結，讓客戶得以尋求協助。  |
| 可用性/執行時間                  | 應用程式的執行時間必須至少達到 99.9%。 |
|  |  |


## <a name="business-requirements"></a>商業需求

商業需求包含下列程序、契約，和法律責任：

* 您必須在 [Microsoft 合作夥伴網路 (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) 上註冊，或成為已註冊的雲端 Marketplace 發行者。 如果您未註冊，請依照[成為雲端 Marketplace 發行者](../../become-publisher.md)中的步驟操作。  (執行第三個步驟時，請改用 [AppSource 合作夥伴提名表單](https://appsource.microsoft.com/partners/signup))。 

    >[!NOTE]
    >您需要使用與註冊 Microsoft 開發人員中心相同的帳戶來登入 Cloud Partner 入口網站。 您所有的 Azure Marketplace 供應項目應該只會共有一個 Microsoft 帳戶。 此帳戶不應該限定於特定個別服務或供應項目。

* AppSource 未提供商務用發佈選項，因此您必須使用目前的訂購和帳單基礎架構，而無需其他投資或變更。
* 您必須以合乎商業行為的方式，負責為客戶提供技術支援。 此支援可以免費、收費或透過社群提供。
* 您必須負責為您的軟體和任何第三方廠商相依性進行授權。
* 您應已建立相關聯的行銷附隨品，例如官方應用程式名稱、描述 (採用 HTML 格式)、PNG 格式的標誌影像 (40 x 40、90x90、115 x 115 和 255 x 115 像素)，以及使用規定和隱私權原則。  


## <a name="next-steps"></a>後續步驟

符合這些需求後，您即可[建立 Dynamics 365 Customer Engagement 供應項目](./cpp-create-offer.md) 
