---
title: 將有限的試用版專案移至 Azure
titleSuffix: Azure Cognitive Services
description: 瞭解如何將有限的試用版專案移至 Azure。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 22c3767dfac1e377890f1e01517d18263e694854
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560926"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>如何將有限的試用版專案移至 Azure

由於自訂視覺服務已完成移至 Azure, 因此在 Azure 外部的有限試用專案支援即將結束。 本檔將示範如何使用自訂視覺 Api, 將有限的試用版專案複製到 Azure 資源。

支援在[自訂視覺網站](https://customvision.ai)上觀看有限的試用專案, 2019 年3月25日結束。 本檔現在會說明如何搭配 GitHub 上的[遷移 python 腳本](https://github.com/Azure-Samples/custom-vision-move-project)使用自訂視覺 api), 將您的專案複製到 Azure 資源。

如需更多詳細資料, 包括有限試用版淘汰程式中的重要期限, 請參閱[版本](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019)資訊或傳送給有限試用版專案之擁有者的電子郵件通訊。

[遷移腳本](https://github.com/Azure-Samples/custom-vision-move-project)可讓您在目前的反復專案中下載並上傳所有標記、區域和影像, 以重新建立專案。 這會讓您在新的訂用帳戶中保留新的專案, 然後您可以進行定型。

## <a name="prerequisites"></a>先決條件

- 您將需要與您要用來登入[自訂視覺網站](https://customvision.ai)的 Microsoft 帳戶或 AZURE ACTIVE DIRECTORY (AAD) 帳戶相關聯的有效 Azure 訂用帳戶。 
    - 如果您沒有 Azure 帳戶, 請建立免費[帳戶](https://azure.microsoft.com/free/)。
    - 如需訂用帳戶和資源的 Azure 概念的簡介，請參閱[Azure 開發人員指南](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)。
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 入口網站中建立自訂視覺資源

若要使用自訂視覺服務搭配 Azure, 您必須在[Azure 入口網站](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)中建立自訂視覺定型和預測資源。 

多個專案可以與單一資源相關聯。 [定價和限制](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)的詳細資料可供使用。 若要繼續免費使用自訂視覺服務, 您可以在 Azure 入口網站中選取 [F0] 層。 

> [!NOTE]
> 當您將自訂視覺專案移至 Azure 資源時, 它會繼承該 Azure 資源的基礎[許可權]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。 如果您組織中的其他使用者是您專案所在之 Azure 資源的擁有者, 他們就能夠在[自訂視覺網站](https://customvision.ai)上存取您的專案。 同樣地, 刪除您的資源將會刪除您的專案。  

## <a name="find-your-limited-trial-project-information"></a>尋找您的有限試用版專案資訊

若要移動您的專案, 您將需要您嘗試要遷移之專案的_專案識別碼_和_訓練索引鍵_。 如果您沒有這項資訊, 請造訪[https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects)以取得每個專案的識別碼和金鑰。 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>使用 Python 範例程式碼, 將您的專案複製到 Azure

遵循[範例程式碼指示](https://github.com/Azure-Samples/custom-vision-move-project), 使用有限的試用版金鑰和專案識別碼作為「來源」資料, 以及您建立為「目的地」的新 Azure 資源的金鑰。

根據預設, 所有有限的試用專案都會裝載于美國中南部 Azure 區域。

## <a name="next-steps"></a>後續步驟

您的專案現在已移至 Azure 資源。 您必須在任何已撰寫的應用程式中更新您的定型和預測金鑰。

若要在[自訂視覺網站](https://customvision.ai)上查看您的專案, 請使用您用來登入 Azure 入口網站的相同帳戶登入。 如果看不到您的專案, 請確認您位於[自訂視覺網站](https://customvision.ai)中的目錄, 做為您的資源在 Azure 入口網站中所在的目錄。 在 [Azure 入口網站] 和 [CustomVision.ai] 中, 您可以從畫面右上角的下拉式使用者功能表選取您的目錄。