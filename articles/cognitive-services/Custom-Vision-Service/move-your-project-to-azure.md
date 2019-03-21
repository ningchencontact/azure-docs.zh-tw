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
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821297"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>如何將有限的試用版專案移至 Azure 中使用 CustomVision.ai 站台


因為 Custom Vision Service 目前處於[Azure Preview](https://azure.microsoft.com/services/preview/)，對 Azure 外部的有限的試用版專案的支援即將結束。 本文會教導您如何使用[自訂視覺網站](https://customvision.ai)將有限的試用版專案，以與 Azure 資源相關聯。 

> [!NOTE]
> 當您將您的自訂視覺專案移至 Azure 的資源，它們繼承基礎[權限]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)該 Azure 資源。 如果您的組織中的其他使用者的專案是在 Azure 資源的擁有者，他們將能夠存取您的專案上[自訂視覺網站](https://customvision.ai)。 同樣地，刪除您的資源將會刪除您的專案。  


如需訂用帳戶和資源的 Azure 概念的簡介，請參閱[Azure 開發人員指南。](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>必要條件

您需要有效 Azure 訂用帳戶相同的 Microsoft 帳戶或您用來登入的 Azure Active Directory (AAD) 帳戶相關聯[自訂視覺網站](https://customvision.ai)。 

如果您沒有 Azure 帳戶[建立帳戶](https://azure.microsoft.com/free/)免費。


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 入口網站中建立自訂視覺資源
若要搭配 Azure 使用 Custom Vision Service，您必須建立自訂視覺定型和預測資源[Azure 入口網站](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)。 

 若要移動您的專案使用這[自訂視覺網站](https://customvision.ai)體驗，您必須在美國中南部區域中，建立您的資源，因為有限的試用版的所有專案都在美國中南部。 

多個專案可以是單一資源相關聯。 更多詳細[價格和限制](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)可用。 若要繼續免費使用 Custom Vision Service，您可以在 Azure 入口網站中選取 F0 層。 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>將有限的試用版專案移動至 Azure 資源

1.  在網頁瀏覽器，瀏覽至[自訂視覺網站](https://customvision.ai)，然後選取__登入__。 開啟您想要移轉的 Azure 帳戶的專案。 
2.  按一下畫面頂端右下角的齒輪圖示，以開啟專案的 [設定] 頁面。 

    ![專案設定為 [專案] 頁面的右上方的齒輪圖示。](./media/move-your-project-to-azure/settings-icon.png)


3. 按一下 __移至 Azure__。

    ![移至 Azure 按鈕位於左下角的 專案設定 頁面。](./media/move-your-project-to-azure/move-to-azure.jpg)


4. 從下拉式清單上__移至 Azure__按鈕，選取您想要移至您專案的 Azure 資源。 按一下 __移動__。 

5. 如果看不到您稍早建立的自訂視覺服務的 Azure 資源，它可能是另一個目錄中。 若要將您的專案移到另一個目錄中的資源，請遵循下面的指示。 

    ![專案的移轉時段。](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>將專案移到另一個 Azure 目錄 

> [!NOTE]
> 在 Azure 入口網站和 CustomVision.ai，您可以從畫面的右上角的使用者 下拉式功能表中選取您的目錄。   


1. 識別您的 Azure 資源位於哪個目錄。 您可以找到您在 Azure 入口網站功能表列的右上方的使用者名稱底下所列的目錄。 

    ![您的目錄會列在 Azure 入口網站功能表列的右上方的使用者名稱。 上也提供本文中使用的原始碼。](./media/move-your-project-to-azure/identify_directory.jpg)

2. 尋找您的自訂辨識訓練資源的資源識別碼。 您可以在 Azure 入口網站中找到此開啟您的自訂辨識訓練資源，然後選取 [資源管理] 區段下的 [屬性]。 會有您的資源識別碼。 

    ![在 Azure 入口網站中尋找您的資源識別碼，請開啟您的自訂辨識訓練資源，並選取 [資源管理] 區段下的 [屬性]。](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. 或者，您可以在這裡找到您自訂的視覺資源的資源識別碼中的自訂視覺網站直接[設定頁面]( https://www.customvision.ai/projects#/settings)。 您必須切換到您的 Azure 資源位於相同的目錄。

    ![針對每個資源，您可以在自訂視覺網站上的 [設定] 頁面上，會列出您的資源識別碼。](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. 您已經有您的資源識別碼，則會傳回至您嘗試從有限的試用版移至 Azure 資源的自訂視覺專案。 提醒您，您可能需要切換回您的原始目錄以尋找它。 遵循提供的指示[上面](#move-your-limited-trial-project-to-an-azure-resource)來開啟專案的 [設定] 頁面並選取__移動至 Azure__。 


5. 在移至 Azure 視窗中，檢查 移動至不同的 Azure 目錄？ 方塊。 選取您想要移至專案，然後輸入您要移動您的專案資源的資源識別碼的目錄。 按一下 __移動__。 



5. 請記住，您的專案現在是在不同的目錄。 若要尋找您的專案，您必須切換到您的專案是在自訂視覺 web 入口網站上相同的目錄。 在這兩種 Azure 入口網站中，[自訂視覺網站](https://customvision.ai)，您可以從畫面的右上角的下拉式清單的帳戶 功能表中選取您的目錄。 

## <a name="next-steps"></a>後續步驟

您的專案現在已移至 Azure 資源。 您必須更新您的定型和預測金鑰，任何您所撰寫的應用程式中。
