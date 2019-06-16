---
title: Azure 應用程式供應項目必要條件 |Azure Marketplace
description: 在 Azure Marketplace 上發佈 Azure 供應項目的先決條件。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 64039234a3863332ca19b915fb59a5271625d695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258192"
---
# <a name="azure-application-prerequisites"></a>Azure 應用程式先決條件

本文說明在 Azure Marketplace 上發佈受控應用程式供應項目的先決條件。  如果您尚未這樣做，請檢閱下列資訊來源：
- 請根據您的 SKU 類型[Azure 應用程式：解決方案範本提供發佈指南](../../marketplace-solution-templates.md)或[Azure 應用程式：受管理的應用程式發行指南的供應項目](../../marketplace-managed-apps.md)
- [建置解決方案範本及受管理的應用程式在 Azure marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)影片


## <a name="technical-requirements"></a>技術需求

技術需求包含下列項目︰

*   如需 Azure Resource Manager 的詳細資訊，請參閱[了解 Azure Resource Manager 範本的結構和語法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 本文說明 Azure Resource Manager 範本的結構。 它會呈現範本的不同區段，以及這些區段中可用的屬性。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。 
* Azure 快速入門範本。<br> 如需詳細資訊，請參閱

  * [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。 透過 Azure Resource Manager，利用社群貢獻的範本部署 Azure 資源，協助您完成更多的工作。 Azure 資源管理員可讓您使用宣告式範本佈建應用程式。 您只需要使用一個範本，就能部署多項服務及其相依項目。 您可以使用相同的範本，在應用程式生命週期的每個階段重複部署應用程式。
  * [GitHub：Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)。 此報告包含由社群所提供所有目前可用的 Azure Resource Manager 範本。 在 https://azure.microsoft.com/documentation/templates/ 中維護了可搜尋的範本索引。
* 建立 UI 定義<br>
如需詳細資訊，請參閱[為您的受控應用程式建立 Azure 入口網站使用者介面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。 本文介紹 createUiDefinition.json 檔案的核心概念。 Azure 入口網站會使用這個檔案，產生用來建立受控應用程式的使用者介面。


## <a name="business-requirements"></a>商業需求

商業需求包含下列程序、契約，和法律責任：

* 您必須是已註冊的雲端 Marketplace 發行者。 如果您未註冊，請依照下列文章中的步驟[成為雲端 Marketplace 發行者](https://docs.microsoft.com/azure/marketplace/become-publisher
)。

>[!NOTE]
>您需要使用與註冊 Microsoft 開發人員中心相同的帳戶來登入 Cloud Partner 入口網站。 您所有的 Azure Marketplace 供應項目應該只會共有一個 Microsoft 帳戶。 此帳戶不應該限定於特定個別服務或供應項目。

* 您的公司 （或其子公司） 必須位於銷售-從-國家/地區支援 Azure marketplace。 目前的國家/地區清單，請參閱 < [Microsoft Azure Marketplace 參與原則](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)。
* 您的產品授權，必須與 Azure Marketplace 支援的計費模式相容。 如需詳細資訊，請參閱 [Azure Marketplace 的計費選項](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations)。
* 您必須以合乎商業行為的方式，負責為客戶提供技術支援。 此支援可以免費、收費或透過社群提供。
* 您必須負責為您的軟體和任何第三方廠商相依性進行授權。
* 為了使您的供應項目可列於 Microsoft Azure Marketplace 和 Microsoft Azure 入口網站中，您提供的內容必須符合標準。
* 您必須同意 Microsoft Azure Marketplace 參與原則和發行者合約中的條款。
* 您必須遵守 Microsoft Azure 網站使用規定、Microsoft 隱私權聲明，以及 Microsoft Azure 認證方案合約。


## <a name="publishing-requirements"></a>發佈需求

若要發佈新的 Azure 應用程式供應項目，您必須符合下列必要條件：

* 備妥中繼資料以供使用。 下列清單 (不完整) 會顯示此中繼資料的範例：
  * 標題
  * 描述 (HTML 格式)
  * 標誌影像 (PNG 格式) 及這些固定的影像大小：40 x 40 像素、90 x 90 像素、115 x 115 像素和 255 x 115 像素。
* A*使用條款*並*隱私權原則*文件
* 應用程式文件
* 支援連絡人


## <a name="next-steps"></a>後續步驟

一旦符合所有需求之後，就能開始[建立 Azure 應用程式供應項目](./cpp-create-offer.md)。 
 
