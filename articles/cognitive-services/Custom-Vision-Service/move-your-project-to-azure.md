---
title: 將有限的試用版專案移至 Azure
titlesuffix: Azure Cognitive Services
description: 了解如何將有限的試用版專案移至 Azure。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274445"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>如何將有限的試用版專案移至 Azure

Custom Vision Service 完成其移至 Azure 時，即將結束對 Azure 外部的有限的試用版專案的支援。 本文件將說明如何使用自訂視覺 Api，將有限的試用版專案複製到 Azure 的資源。

支援檢視受限制的試用版的專案上[自訂視覺網站](https://customvision.ai)於 2019 年 3 月 25 日結束。 這份文件現在會顯示您如何使用自訂視覺 Api，與[移轉 python 指令碼](https://github.com/Azure-Samples/custom-vision-move-project)GitHub 上) 複製您的 Azure 資源的專案。

如需詳細資訊，包括有限的試用版已被取代處理序中的索引鍵的期限，請參閱[版本資訊](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019)或傳送給有限的試用版專案的擁有者的電子郵件通訊。

[移轉指令碼](https://github.com/Azure-Samples/custom-vision-move-project)可讓您重新建立專案，藉由下載，然後上傳的所有標記，區域，以及您目前的反覆項目中的映像。 它會讓您有新的專案，您可以訓練的新訂用帳戶。

## <a name="prerequisites"></a>必要條件

- 您需要有效的 Azure 訂閱的 Microsoft 帳戶或您想要用來登入 Azure Active Directory (AAD) 帳戶相關聯[自訂視覺網站](https://customvision.ai)。 
    - 如果您沒有 Azure 帳戶[建立帳戶](https://azure.microsoft.com/free/)免費。
    - 如需訂用帳戶和資源的 Azure 概念的簡介，請參閱[Azure 開發人員指南](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)。
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 入口網站中建立自訂視覺資源

若要搭配 Azure 使用 Custom Vision Service，您必須建立自訂視覺定型和預測資源[Azure 入口網站](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)。 

多個專案可以是單一資源相關聯。 更多詳細[價格和限制](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)可用。 若要繼續免費使用 Custom Vision Service，您可以在 Azure 入口網站中選取 F0 層。 

> [!NOTE]
> 當您將您的自訂視覺專案移至 Azure 資源時，它會繼承基礎[權限]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)該 Azure 資源。 如果您的組織中的其他使用者的專案是在 Azure 資源的擁有者，他們將能夠存取您的專案上[自訂視覺網站](https://customvision.ai)。 同樣地，刪除您的資源將會刪除您的專案。  

## <a name="find-your-limited-trial-project-information"></a>尋找您的受限制的試用版專案資訊

若要移動您的專案，您必須_專案識別碼_並_訓練金鑰_您嘗試移轉專案。 如果您沒有這項資訊，請瀏覽[ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects)取得的識別碼和金鑰的每個專案。 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>若要將您的專案複製到 Azure 中使用 Python 範例程式碼

請遵循[範例程式碼指示](https://github.com/Azure-Samples/custom-vision-move-project)、 使用有限的試用版金鑰，以及專案內的 「 來源 」 資料，以及從 「 目的地 」 為您建立新的 Azure 資源的索引鍵的識別碼。

根據預設，有限的試用版的所有專案都裝載於美國中南部 Azure 區域。

## <a name="next-steps"></a>後續步驟

您的專案現在已移至 Azure 資源。 您必須更新您的定型和預測金鑰，任何您所撰寫的應用程式中。

若要檢視您的專案上[自訂視覺網站](https://customvision.ai)，您用來登入 Azure 入口網站的相同帳戶登入。 如果看不到您的專案，請確認您是在相同的目錄中[自訂視覺網站](https://customvision.ai)做為目錄，在 Azure 入口網站中資源的位置。 在 Azure 入口網站和 CustomVision.ai，您可以從畫面的右上角的使用者 下拉式功能表中選取您的目錄。