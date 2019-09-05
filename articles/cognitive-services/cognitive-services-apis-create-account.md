---
title: 在 Azure 入口網站中建立認知服務資源
titleSuffix: Azure Cognitive Services
description: 藉由建立及訂閱 Azure 入口網站中的資源，開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274610"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 入口網站建立認知服務資源

使用本快速入門來建立使用 Azure 入口網站的 Azure 認知服務資源。 成功建立認知服務資源之後，您會取得可用來驗證應用程式的端點和金鑰。

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶-[免費建立一個](https://azure.microsoft.com/free/)。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>建立新的 Azure 認知服務資源

建立認知服務資源之前，您必須擁有包含資源的 Azure 資源群組。 當您建立新的資源時，您可以選擇建立新的資源群組，或使用現有的一個。 本文說明如何建立新的資源群組。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [+ 建立資源]。

    ![選取認知服務 API](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 如先前所述，您可以透過兩種方式來建立認知服務資源-使用多服務資源或單一服務資源。

    #### <a name="multi-service-resourcetabmultiservice"></a>[多服務資源](#tab/multiservice)

    若要建立多服務資源，請在搜尋列中輸入**認知服務**。

    ![搜尋認知服務](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    在 [認知服務] 頁面上，選取 [**建立**]。

    ![建立認知服務帳戶](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[單一服務資源](#tab/singleservice)

    若要查看所有可用的認知服務，請選取 [ **Azure Marketplace**] 底下的 [ **AI + Machine Learning**]。 如果您沒有看到您感興趣的服務，請按一下 [**查看全部**]，然後按 [流覽]**認知服務**。 按一下 [**查看更多**] 以查看認知服務的整個目錄。

    一旦您在您感興趣的服務上，請按一下 [**建立**]。
    
    ![選取認知服務 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. 在 [建立] 頁面上，提供下列資訊：

    #### <a name="multi-service-resourcetabmultiservice"></a>[多服務資源](#tab/multiservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如， *MyCognitiveServicesResource*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 請記住您的 Azure 位置，因為您在呼叫 Azure 認知服務時可能需要它。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | 將包含您認知服務資源的 Azure 資源群組。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    按一下 [建立]。

    #### <a name="single-service-resourcetabsingleservice"></a>[單一服務資源](#tab/singleservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如， *TextAnalyticsResource*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 請記住您的 Azure 位置，因為您在呼叫 Azure 認知服務時可能需要它。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | 將包含您認知服務資源的 Azure 資源群組。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen.png)

    按一下 [建立]。

    ***

## <a name="get-the-keys-for-your-resource"></a>取得資源的金鑰

成功建立資源之後，您會在畫面右上方看到快顯通知。 從通知中，按一下 [**移至資源**] 以查看您所建立的認知服務資源。 

![前往認知服務資源](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

從開啟的 [快速入門] 窗格中，您可以存取您的端點和金鑰。

![取得金鑰和端點](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定價層和計費

定價層（以及您收取的金額）是根據您使用驗證資訊傳送的交易數目。 每個定價層都會指定：
* 每秒允許的交易數目上限（TP）。
* 在定價層中啟用的服務功能。
* 預先定義的交易數目成本。 超過此數量會導致您服務的[定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中所指定的額外費用。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除群組中包含的任何其他資源。

若要使用 Azure 入口網站刪除資源群組：

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組] 以顯示資源群組的清單。
2. 找出要刪除的資源群組，然後以滑鼠右鍵按一下清單右側的 [更多] 按鈕（...）。
3. 選取 [刪除資源群組] 並且確認。

## <a name="see-also"></a>另請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
