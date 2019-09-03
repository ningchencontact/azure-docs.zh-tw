---
title: 教學課程：建立您的第一個 ML 實驗：設定
titleSuffix: Azure Machine Learning service
description: 在本教學課程系列中，您會遵循端對端步驟來開始使用在 Jupyter 筆記本中執行的 Azure Machine Learning Python SDK。  第一部分中已建立雲端筆記本伺服器環境及工作區來管理您的實驗和機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: 7ed81375912613995d573b110607e7913adfd10f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051657"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment"></a>教學課程：開始建立您的第一個 ML 實驗

在本教學課程中，您會遵循端對端步驟來開始使用在 Jupyter 筆記本中執行的 Azure Machine Learning Python SDK。 本教學課程是**教學課程系列的第一部分 (共兩部分)** ，其中包含 Python 環境的安裝和設定，以及如何建立工作區來管理您的實驗和機器學習模型。 [**第二部分**](tutorial-1st-experiment-sdk-train.md)會以此為基礎來定型多個機器學習模型，並使用 Azure 入口網站和 SDK 來引進模型管理程序。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立要在下一個教學課程中使用的機器學習工作區。
> * 建立已安裝並預先設定 Azure Machine Learning Python SDK 的雲端式 Jupyter Notebook VM。

## <a name="prerequisites"></a>必要條件

本教學課程的唯一必要條件是 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>建立工作區

工作區是雲端中您用來實驗、定型及部署機器學習模型的基礎資源。 工作區可將您的 Azure 訂用帳戶和資源群組與 SDK 中容易使用的物件結合。 如果您已有 Azure Machine Learning 服務工作區，請跳至[下一節](#azure)。 否則，請建立此工作區。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>建立雲端 Notebook 伺服器

此範例會使用您工作區中的雲端 Notebook 伺服器，讓您擁有免安裝和預先設定的體驗。 如果您想要控制您的環境、套件和相依性，[請使用您自己的環境](how-to-configure-environment.md#local)。

從您的工作區建立雲端資源，以開始使用 Jupyter Notebook。 這項資源是雲端式 Linux 虛擬機器，其已預先設定執行 Azure Machine Learning 服務所需的一切。

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟工作區。  如果您不確定如何在入口網站中找出您的工作區，請參閱如何[尋找您的工作區](how-to-manage-workspace.md#view)。

1. 在 Azure 入口網站中您的工作區頁面上，選取左側的 [Notebook VM]  。

1. 選取 [+新增]  以建立 Notebook VM。

     ![選取新的 VM](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. 為您的 VM 提供名稱。 然後選取 [建立]  。

    > [!NOTE]
    > Notebook 虛擬機器名稱長度必須介於 2 到 16 個字元之間。 有效字元包含字母、數字及 - 字元。  名稱在 Azure 訂用帳戶中必須是唯一的。

1. 請等候狀態變更為 [執行中]  。

### <a name="launch-jupyter-web-interface"></a>啟動 Jupyter Web 介面

在您的 VM 執行之後，使用 [Notebook VM]  區段來啟動 Jupyter Web 介面。

1. 在您 VM 的 [URI]  資料行中選取 [Jupyter]  。

    ![啟動 Jupyter Notebook 伺服器](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    此連結會啟動 Notebook 伺服器，並且在新的瀏覽器索引標籤中開啟 Jupyter Notebook 網頁。此連結只適用於建立 VM 的人員。 工作區的每個使用者都必須建立自己的 VM。

1. 在 Jupyter Notebook 網頁上，上方的資料夾名稱即為您的使用者名稱。  選取此資料夾。

    > [!TIP]
    > 此資料夾位於工作區中的[儲存體容器](concept-workspace.md#resources)上，而不是 Notebook VM 本身。  您可以刪除 Notebook VM，同時保有您所有的工作。  您後續建立新的 Notebook VM 時，將會載入相同的資料夾。 如果您與他人共用您的工作區，你們將會看到彼此的資料夾。

1. 開啟 `samples-*` 子目錄，然後開啟 `tutorials/tutorial-1st-experiment-sdk-train.ipynb`

> [!Warning]
> 請確定您開啟的是 `tutorial-1st-experiment-sdk-train.ipynb` 檔案，而**不是**同名的 `.yml` 檔案。 

在本教學課程的**第二部分**中，您會在 `tutorial-1st-experiment-sdk-train.ipynb` 中執行程式碼以定型機器學習模型。

## <a name="end"></a>清除資源

如果您打算繼續進行本教學課程的**第 2 部分**，請先不要完成這一節。

### <a name="stop-the-notebook-vm"></a>停止 Notebook VM

如果您使用雲端 Notebook 伺服器，當您不使用 VM 時，請停止該 VM 來降低成本。

1. 在您的工作區中，選取 [Notebook VM]  。

   ![停止 VM 伺服器](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. 從清單中選取 VM。

1. 選取 [停止]  。

1. 當您準備好再次使用伺服器時，請選取 [啟動]  。

### <a name="delete-everything"></a>刪除所有內容

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

您也可以保留資源群組，但刪除單一工作區。 顯示工作區屬性，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列工作：

* 建立 Azure Machine Learning 服務工作區。
* 建立及設定您工作區中的雲端 Notebook 伺服器。

請繼續進行本教學課程的**第 2 部分**，以定型簡單的機器學習模型。

> [!div class="nextstepaction"]
> [教學課程：定型您的第一個模型](tutorial-1st-experiment-sdk-train.md)
