---
title: 存取及自訂新的開發人員入口網站-Azure API 管理 |Microsoft Docs
description: 了解如何在 API 管理中使用新的開發人員入口網站。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743571"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>存取及自訂 Azure API 管理中新的開發人員入口網站

這篇文章會示範如何存取新的 Azure API 管理開發人員入口網站。 它會引導您透過視覺化編輯器體驗-新增和編輯的內容，以及自訂網站的外觀。

![新的 API 管理開發人員入口網站](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>先決條件

- 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
- 匯入並發佈 Azure API 管理執行個體。 如需詳細資訊，請參閱[匯入和發佈](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 新的開發人員入口網站目前為預覽狀態。

## <a name="managed-and-self-hosted-versions"></a>受管理和自我裝載的版本

您可以建置您的開發人員入口網站，有兩種：

- **受管理的版本**-作者編輯和自訂內建於入口網站您的 API 管理執行個體並可透過 URL 存取`<your-api-management-instance-name>.developer.azure-api.net`。
- **自我裝載的版本**-藉由部署和自我裝載您 API 管理執行個體外部的入口網站。 這種方法可讓您編輯入口網站的程式碼基底，並延伸提供的核心功能。 如需詳細資訊和指示，請參閱[入口網站的原始碼的 GitHub 儲存機制][1]。

## <a name="access-the-managed-version-of-the-portal"></a>存取入口網站的受管理的版本

請遵循下列步驟來存取入口網站的受管理的版本。

1. 前往 Azure 入口網站中您 API 管理服務執行個體。
1. 按一下 **新的開發人員入口網站 （預覽）** 上方導覽列中的按鈕。 會開啟新的瀏覽器索引標籤，在入口網站的系統管理版本。 如果您第一次存取入口網站，將會自動佈建的預設內容。

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>編輯和自訂入口網站的受管理的版本

在下列影片中，我們會示範如何編輯入口網站的內容、 自訂網站的外觀，以及發行所做的變更。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>後續步驟

深入了解新的開發人員入口網站：

- [含原始碼的 GitHub 存放庫][1]
- [在自我裝載入口網站的指示][2]
- [專案的公開藍圖][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects