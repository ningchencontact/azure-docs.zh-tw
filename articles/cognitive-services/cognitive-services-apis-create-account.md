---
title: 在 Azure 入口網站中建立認知服務帳戶
titlesuffix: Azure Cognitive Services
description: 如何在 Azure 入口網站中建立的 Azure 認知服務 Api 帳戶。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445862"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>建立認知服務帳戶使用 Azure 入口網站

在本快速入門中，您將了解如何註冊 Azure 認知服務，並建立具有單一服務 」 或 「 多服務的訂用帳戶的帳戶。 這些服務都是由 Azure [資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)的形式呈現，可讓您連線到 Azure 認知服務 API 中的一個或多個 API。

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶。 免費[建立帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>建立新的 Azure 認知服務資源

之前建立的資源，您必須使用 Azure 資源群組。 每個認知服務帳戶 （和其相關聯的 Azure 資源） 必須屬於 Azure 資源群組。 當您建立帳戶時，您可以選擇建立新的資源群組，或使用現有的帳戶。 這篇文章會示範如何建立新的資源群組。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [+ 建立資源]  。

    ![選取認知服務 API](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 您可以找到可用的認知服務，使用下列方式：
    * 使用 [搜尋] 列，並輸入您想要訂閱的服務名稱。
        * 若要建立多重服務的訂用帳戶的資源，請輸入**認知服務**在搜尋 列，然後選取**認知服務**資源。

        ![搜尋認知服務](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * 若要查看所有可用的認知服務，請選取**AI + 機器學習服務**下方**Azure Marketplace**。 如果您沒有看到您感興趣的服務，按一下**查看所有**，然後捲動至**認知服務**。 按一下 **更多**檢視整個目錄的認知服務 Api。
    
        ![選取認知服務 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. 在 [建立]  頁面上，提供下列資訊：

    > [!IMPORTANT]
    > 因為您可能需要呼叫 Azure 認知服務時，請記住您的 Azure 位置。

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 我們建議使用描述性的名稱，例如 MyCognitiveServicesAccount  。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **Location** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | [Azure 資源群組](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>取得訂用帳戶金鑰

建立資源後，如果有釘選該資源，就可以在 Azure 儀表板上直接存取。 若未釘選，可以在 [資源群組]  中找到。 選取您的資源之後, 您可以取得的金鑰，方法是選取**金鑰**下方**資源管理**。

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除的認知服務訂用帳戶，您可以刪除資源或資源群組。 刪除資源群組時，也會刪除資源群組相關聯的任何其他資源。

若要使用 Azure 入口網站刪除資源群組：

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組]  以顯示資源群組的清單。
2. 找出要刪除資源群組，並以滑鼠右鍵按一下 [更多資訊] 按鈕 （...） 右邊的清單。
3. 選取 [刪除資源群組]  並且確認。

## <a name="see-also"></a>請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [Azure 認知服務有哪些？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
