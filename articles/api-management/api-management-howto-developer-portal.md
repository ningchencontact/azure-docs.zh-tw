---
title: 存取和自訂新的開發人員入口網站-Azure API 管理 |Microsoft Docs
description: 瞭解如何在 API 管理中使用新的開發人員入口網站。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: c015b1afbc61e1501e656aaa480ee2a4e19ba094
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672796"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>在 Azure API 管理中存取及自訂新的開發人員入口網站

本文說明如何存取新的 Azure API 管理開發人員入口網站。 它會逐步引導您完成視覺效果編輯器的體驗-新增和編輯內容，以及自訂網站的外觀。

![新增 API 管理開發人員入口網站](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> 必要條件

- 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
- 匯入並發佈 Azure API 管理執行個體。 如需詳細資訊，請參閱[匯入和發佈](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 新的開發人員入口網站目前為預覽狀態。

## <a name="managed-vs-self-hosted"></a>受控和自我裝載版本

您可以透過兩種方式來建立開發人員入口網站：

- **受控版本**-藉由編輯和自訂入口網站（內建于您的 API 管理實例中），並可透過 `<your-api-management-instance-name>.developer.azure-api.net` 的 URL 存取。
- **自我裝載版本**-藉由在 API 管理實例外部部署和自我裝載您的入口網站。 這種方法可讓您編輯入口網站的程式碼基底，並擴充提供的核心功能。 如需詳細資訊和指示，請參閱[GitHub 存放庫，並提供入口網站的原始程式碼][1]。

## <a name="managed-access"></a>存取入口網站的受管理版本

請遵循下列步驟來存取受管理的入口網站版本。

1. 在 Azure 入口網站中，移至您的 API 管理服務實例。
1. 按一下頂端導覽列中的 [**新開發人員入口網站（預覽）** ] 按鈕。 具有入口網站管理版本的新瀏覽器索引標籤隨即開啟。 如果您是第一次存取入口網站，則會自動布建預設內容。

## <a name="managed-tutorial"></a>編輯和自訂入口網站的受控版本

在下列影片中，我們會示範如何編輯入口網站的內容、自訂網站外觀，以及發佈變更。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>常見問題

在本節中，我們會回答有關新開發人員入口網站的常見問題，這些是一般性質的問題。 如需自我裝載版本的特定問題，請參閱[GitHub 存放庫的 wiki 一節](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>如何將內容從舊的開發人員入口網站遷移到新的版本？

你不能。 入口網站不相容。

### <a name="when-will-the-portal-become-generally-available"></a>入口網站何時會正式推出？

入口網站目前為預覽狀態，且會在日曆年度結束時正式推出（2019）。 預覽版本不應用於生產用途。

### <a name="will-the-old-portal-be-deprecated"></a>舊的入口網站會被取代嗎？

是，在新的正式推出之後，它將會被取代。 如果您有任何疑慮，請[在專屬的 GitHub 問題中](https://github.com/Azure/api-management-developer-portal/issues/121)提出。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新的入口網站是否具有舊版入口網站的所有功能？

正式運作的目標是提供以案例為基礎的功能，與舊的入口網站進行同位檢查。 在那之前，預覽版本可能未執行選取的功能。

例外狀況是舊版入口網站中的*應用程式*和*問題*，在新的入口網站中將無法使用。 如果您在舊的入口網站中使用*問題*，並在新的專案中需要它們，請在[專用的 GitHub 問題](https://github.com/Azure/api-management-developer-portal/issues/122)中張貼留言。

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>我發現錯誤和（或）我想要要求功能。

太棒了！ 您可以提供意見反應、提交功能要求，或透過[GitHub 存放庫的問題一節](https://github.com/Azure/api-management-developer-portal/issues)提出錯誤報表。 當您在那裡時，我們也會感謝您對以 `community` 標籤標示的問題提供意見反應。

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>我想要在環境之間移動新入口網站的內容。 我該如何做，我需要使用自我裝載版本？

您可以在兩個入口網站版本管理和自我裝載中執行此動作。 新的開發人員入口網站支援透過 API 管理服務的管理 API 來解壓縮內容。 Api 記載[于 GitHub 存放庫的 wiki 區段中](https://github.com/Azure/api-management-developer-portal/wiki/)。 我們也撰寫了可協助您開始[使用的腳本](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)。

我們仍在努力將此程式與 API 管理 DevOps 資源套件進行協調。

### <a name="what-do-i-need-to-configure-for-the-new-portal-to-work-in-my-api-management-service-in-vnet"></a>我需要設定新的入口網站，才能在 VNET 中的 API 管理服務中工作嗎？

當新的開發人員入口網站處於預覽狀態時，您需要允許連線到美國西部區域中的 Azure 儲存體服務，才能讓受管理的入口網站在 VNET 的 API 管理服務中工作。 如需詳細資訊，請[參閱儲存體檔](../storage/common/storage-network-security.md#available-virtual-network-regions)。

在新的入口網站正式推出之後，將不再需要上述安裝程式。

視您的設定而定，入口網站的自我裝載版本可能需要額外的連線設定。

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>如何在建立新*頁面*時選取*版面*配置？

*版面*配置會藉由將其 url 範本與*頁面的*url 進行比對，套用至頁面。 例如，具有 `/wiki/*` 之 URL 範本的配置，將會套用*到具有 @no__t* -3 區段的每個*頁面*： `/wiki/getting-started`、`/wiki/styles` 等等。

### <a name="why-doesnt-the-interactive-developer-console-work"></a>為什麼互動式開發人員主控台無法正常執行？

這可能與 CORS 相關。 互動式主控台會從瀏覽器提出用戶端 API 要求。 您可以藉由在您的 API 上新增[cors 原則](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)來解決 CORS 問題。 您可以手動指定所有參數（例如，原點為 https://contoso.com) ，或使用萬用字元 `*` 值。

## <a name="next-steps"></a>後續步驟

深入瞭解新的開發人員入口網站：

- [具有原始程式碼的 GitHub 存放庫][1]
- [有關自我裝載入口網站的指示][2]
- [專案的公開藍圖][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects