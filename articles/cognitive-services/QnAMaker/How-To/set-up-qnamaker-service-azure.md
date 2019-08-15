---
title: 設定 QnA Maker 服務 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967660"
---
# <a name="create-a-qna-maker-service"></a>建立 QnA Maker 服務

您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。

## <a name="create-a-new-service"></a>建立新的服務

此程式會部署一些 Azure 資源。 這些資源會一同管理知識庫內容，並透過端點提供問答功能。

1. 登入 Azure 入口網站並[建立 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)資源。

1. 閱讀條款及條件之後, 請選取 [**建立**]。

    ![建立新的 QnA Maker 服務](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 **QnA Maker** 中，選取適當的層級和地區。

    ![建立新的 QnA Maker 服務 - 定價層和區域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 在 [名稱] 中填入識別此 QnA Maker 服務的唯一名稱。 此名稱也會識別將與知識庫產生關聯的 QnA Maker 端點。
    * 選擇將部署 QnA Maker 資源的**訂用帳戶**。
    * 選取 QnA Maker 管理服務 (入口網站和管理 Api) 的**定價層**。 關於 SKU 價格的詳細資料，請參閱[這裡](https://aka.ms/qnamaker-pricing)。
    * 建立新的**資源群組** (建議選項)，或使用部署此 QnA Maker 資源的現有資源群組。 QnA Maker 會建立數個 Azure 資源;當您建立資源群組來保存這些資源時, 您可以使用資源組名輕鬆地尋找、管理和刪除這些資源。
    * 選取**資源群組位置**。
    * 選擇 Azure 搜尋服務的 [定價層搜尋]。 如果您看到免費層選項呈現灰色，表示您已經有免費的 Azure 搜尋層部署於您的訂用帳戶中。 在此情況下，您必須先開始使用基本 Azure 搜尋服務層。 關於 Azure 搜尋服務定價的詳細資料，請參閱[這裡](https://azure.microsoft.com/pricing/details/search/)。
    * 選擇您要部署 Azure 搜尋服務資料的**搜尋位置**。 必須儲存客戶資料的限制會通知您對於 Azure 搜尋服務選擇的位置。
    * 在 [應用程式名稱] 中，設定應用程式服務的名稱。
    * 應用程式服務預設為標準 (S1) 層。 您可以在建立之後變更方案。 如需應用程式服務定價的詳細資訊，請參閱[這裡](https://azure.microsoft.com/pricing/details/app-service/)。
    * 選擇將部署應用程式服務的**網站位置**。

        > [!NOTE]
        > 搜尋位置可能與網站位置不同。

    * 選擇您是否要啟用 **Application Insights**。 如果啟用 **Application Insights**，QnA Maker 會收集流量、交談記錄和錯誤的遙測資料。
    * 選擇將部署 Application Insights 資源的**App insights 位置**。
    * 針對成本節約量值, 您可以[共用](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker)部分 (而非所有) 為 QnA Maker 建立的 Azure 資源。 

1. 所有欄位都經過驗證後, 您就可以選取 [**建立**], 開始在您的訂用帳戶中部署這些服務。 這需要幾分鐘才能完成。

1. 完成部署後，您會看到在您的訂用帳戶中已建立下列資源。

    ![建立新 QnA Maker 服務的資源](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>管理服務的區域

QnA Maker 的管理服務 (僅用於初始資料處理的入口網站 &) 僅適用于美國西部。 此美國西部服務中不會儲存任何客戶資料。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立和發佈知識庫](../Quickstarts/create-publish-knowledge-base.md)
