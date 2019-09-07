---
title: 在入口網站中建立 Azure ML 工作區
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 入口網站中建立、檢視及刪除 Azure Machine Learning services 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: df015df3c20cdb99512167e3dc3e455da2762c52
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744794"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces-in-the-azure-portal"></a>建立和管理 Azure 入口網站中的 Azure Machine Learning 服務工作區

在此文章中，您將在 Azure 入口網站中為 [Azure Machine Learning services](overview-what-is-azure-ml.md) 建立、檢視和刪除 [**Azure Machine Learning services 工作區**](concept-workspace.md)。  入口網站是開始使用工作區的最簡單方式，但隨著您的需求變更或自動化的需求增加，您也可以[使用 CLI](reference-azure-machine-learning-cli.md)、Python 程式[代碼](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或透過[VS Code 延伸](how-to-vscode-tools.md#get-started-with-azure-machine-learning)模組來建立和刪除工作區。

## <a name="create-a-workspace"></a>建立工作區

若要建立工作區，您將需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>下載設定檔

1. 如果您將建立[筆記本 VM](tutorial-1st-experiment-sdk-setup.md#azure)，請略過此步驟。

1. 如果您打算在參考此工作區的本機環境上使用程式碼， 請從工作區的 [概觀] 區段中，選取 [下載 config.xml]。  

   ![下載 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 指令碼或 Jupyter Notebook 將文件置於目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。 當您建立筆記本 VM 時，會為您將此檔案新增至 VM 上的正確目錄。


## <a name="view"></a>查看工作區

1. 在入口網站的左上角，選取 [所有服務]。

1. 在 [**所有服務**] 篩選欄位中，輸入**machine learning 服務**。  

1. 選取 [ **Machine Learning 服務工作區**]。

   ![搜尋 Azure Machine Learning 服務工作區](media/how-to-manage-workspace/all-services.png)

1. 查看找到的工作區清單。 您可以根據訂用帳戶、資源群組和位置來篩選。  

1. 選取工作區以顯示其屬性。
   ![工作區屬性](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>刪除工作區

在您要刪除的工作區頂端，使用 [刪除] 按鈕。

  ![刪除按鈕](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

遵循完整的教學課程，了解如何使用工作區來建立、訓練和部署使用 Azure Machine Learning 服務的模型。

> [!div class="nextstepaction"]
> [教學課程：將模型定型](tutorial-train-models-with-aml.md)
