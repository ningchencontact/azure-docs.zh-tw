---
title: 建立 Dynamics 365 for Customer Engagement 技術資產 - Azure Marketplace | Microsoft Docs
description: 建立 Dynamics 365 for Customer Engagement 應用程式供應項目的技術資產。
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 50c4fd512206cdf17ebb555acb88de2a3f74c2bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472663"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>建立 Azure 應用程式供應項目的技術資產

您通常會使用 [Dynamics 365 for Customer Engagement 應用程式 SDK](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk) 來開發解決方案。  解決方案可採用各種不同的形式，如 [Dynamics 365 for Customer Engagement 應用程式的程式設計模型](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models)所說明。  請選擇最符合您解決方案需求的形式。  開發解決方案時，您必須處理多項問題，例如擴充性的選擇、解決方案元件和版本相容性等。  如需詳細資訊，請參閱[解決方案簡介](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions)。

發佈至 AppSource 的 Dynamics 365 解決方案的大多都是以套件檔案形式散發的受控應用程式。


## <a name="creating-and-storing-the-package"></a>處理和儲存套件

您可以在[在 AppSource 上發佈應用程式](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource)一節中找到建立 Dynamics 365 for Customer Engagement 供應項目的對等文件。  以下包含的主題將詳細說明如何建立解決方案套件檔案，並將其上傳至 Azure 儲存體：

- [步驟 4：建立應用程式的 AppSource 套件](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) - 說明如何建立代表受控應用程式的壓縮 (zip) 檔案，其中包含：解決方案資產資料夾、自訂程式碼 DLL、MIME 類型資訊檔案、AppSource 套件圖示、授權條款 (HTML) 檔案和內容檔案 (XML)。
- [步驟 5：在 Azure 儲存體上儲存 AppSource 套件並使用 SAS 金鑰產生 URL](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) - 說明如何將 AppSource 套件檔案儲存在 Microsoft Azure Blob 儲存體帳戶中，並使用共用存取簽章 (SAS) 金鑰來共用套件檔案。 系統會從您的 Azure 儲存體位置擷取套件檔案進行認證，然後將其用於 AppSource 試用和發佈。


## <a name="next-steps"></a>後續步驟

如果您尚未[建立 Dynamics 365 for Customer Engagement 供應項目](./cpp-create-offer.md)，請加以建立。  然後，您就可以開始[發佈供應項目](./cpp-publish-offer.md)。
