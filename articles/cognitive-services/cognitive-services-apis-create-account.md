---
title: 在 Azure 入口網站中建立認知服務 API 帳戶
titlesuffix: Azure Cognitive Services
description: 如何在 Azure 入口網站中建立 Microsoft 認知服務 API 帳戶。
services: cognitive-services
author: garyericson
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 37f53303a3b0c224c1286fb488a796fd5cdee0e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386411"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立認知服務帳戶

透過本快速入門開始使用 Azure 認知服務。 這些服務都是由 Azure [資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)的形式呈現，可讓您連接到眾多可用認知服務 API 中的一個或多個 API。

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶。 您可以免費[建立帳戶](https://azure.microsoft.com/free/)。

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>建立並訂閱 Azure 認知服務資源

1. 登入 [Azure 入口網站](http://portal.azure.com)，然後按一下 [+ 建立資源]。
    
    ![選取認知服務 API](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. 在 [Azure Marketplace] 底下，選取 [AI + 機器學習服務]。 如果您沒看到感興趣的服務，請按一下 [查看全部]，即可檢視認知服務 API 的完整目錄。

    ![選取認知服務 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. 在 [建立] 頁面上，提供下列資訊：

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 我們建議使用描述性的名稱，例如 *MyNameFaceAPIAccount*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | [Azure 資源群組](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group)，將包含您的認知服務資源。 您可以建立新的群組，或將其新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>存取您的資源 

> [!NOTE]
> 訂用帳戶擁有者可以藉由套用 [Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)、指派「不允許的資源類型」原則定義，以及指定 **Microsoft.CognitiveServices/accounts** 作為目標資源類型，針對資源群組或訂用帳戶停用認知服務帳戶的建立。

建立資源後，如果有釘選該資源，就可以在 Azure 儀表板上直接存取。 若未釘選，可以在 [資源群組]中找到。

在認知服務資源中，您可以使用 [概觀] 區段中的端點 URL 和金鑰，開始在應用程式中進行 API 呼叫。

![資源畫面](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [電腦視覺 API C# 教學課程](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>另請參閱

* [快速入門：從影像擷取手寫文字](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [教學課程：建立應用程式來偵測並框出影像中的臉部](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [建置自訂搜尋網頁](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [使用 Bot Framework 整合 Language Understanding (LUIS) 和 Bot](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
