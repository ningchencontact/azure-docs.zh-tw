---
title: 在 Azure 入口網站中建立認知服務資源
titlesuffix: Azure Cognitive Services
description: 藉由建立及訂閱 Azure 入口網站中的資源, 開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: e9616f3014288e4b2580f474c49c646928db5a08
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334237"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 入口網站建立認知服務資源

使用本快速入門來開始使用 Azure 入口網站的 Azure 認知服務。 認知服務是以您在 Azure 訂用帳戶中建立的 Azure[資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)來表示。 建立資源之後, 請使用為您產生的金鑰和端點來驗證您的應用程式。 

## <a name="prerequisites"></a>先決條件

* 有效的 Azure 訂用帳戶-[免費建立一個](https://azure.microsoft.com/free/)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>建立新的 Azure 認知服務資源

建立認知服務資源之前, 您必須擁有包含資源的 Azure 資源群組。 當您建立新的資源時, 您可以選擇建立新的資源群組, 或使用現有的一個。 本文說明如何建立新的資源群組。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [+ 建立資源]。

    ![選取認知服務 API](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 您可以使用下列方式, 在中尋找可用的認知服務:
    * 使用 [搜尋] 列, 並輸入您想要訂閱的服務名稱。
        * 若要建立多服務資源, 請在搜尋列中輸入**認知服務**, 然後選取**認知服務**資源。

        ![搜尋認知服務](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * 若要查看所有可用的認知服務, 請選取 [ **Azure Marketplace**] 底下的 [ **AI + Machine Learning**]。 如果您沒有看到您感興趣的服務, 請按一下 [**查看全部**], 然後按 [流覽]**認知服務**。 按一下 [**更多**] 以查看認知服務 API 的整個目錄。
    
        ![選取認知服務 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. 在 [建立] 頁面上，提供下列資訊：

    > [!IMPORTANT]
    > 請記住您的 Azure 位置, 因為您在呼叫 Azure 認知服務時可能需要它。

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如*MyCognitiveServicesAccount*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **Location** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | [Azure 資源群組](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>取得資源的金鑰

建立資源後，如果有釘選該資源，就可以在 Azure 儀表板上直接存取。 若未釘選，可以在 [資源群組]中找到。 選取您的資源之後, 您可以在 [**資源管理**] 底下選取 [**金鑰**] 來取得金鑰。

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除群組中包含的任何其他資源。

若要使用 Azure 入口網站刪除資源群組：

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組] 以顯示資源群組的清單。
2. 找出要刪除的資源群組, 然後以滑鼠右鍵按一下清單右側的 [更多] 按鈕 (...)。
3. 選取 [刪除資源群組] 並且確認。

## <a name="see-also"></a>另請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
