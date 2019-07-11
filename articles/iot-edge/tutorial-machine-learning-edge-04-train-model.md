---
title: 對模型進行定型和部署 - Azure IoT Edge 上的 Machine Learning | Microsoft Docs
description: 使用 Azure Machine Learning 來對機器學習模型進行定型，然後將該模型封裝成可部署為 Azure IoT Edge 模組的容器映像。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: acf0b1984eb3e68858be6ed68731612448e672f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432697"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>教學課程：定型和部署 Azure Machine Learning 模型

> [!NOTE]
> 此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 如果您是被直接引導至這篇文章，我們建議您先從本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)開始，以取得最佳成效。

在此文章中，我們會先使用 Azure Notebooks 來使用 Azure Machine Learning 對機器學習模型進行定型，然後將該模型封裝成可部署為 Azure IoT Edge 模組的容器映像。 Azure Notebooks 能運用 Azure Machine Learning 服務工作區，其為用來對機器學習模型進行實驗、定型及部署的基本區塊。

此教學課程這個部分中的活動已被分散至兩個筆記本之上。

* **01-turbofan\_regression.ipynb：** 此筆記本能逐步引導您完成使用 Azure Machine Learning 對模型進行定型和發佈的步驟。 一般而言，所涉及的步驟如下：

  1. 下載、準備並探索定型資料
  2. 使用服務工作區來建立並執行機器學習實驗
  3. 評估實驗的模型結果
  4. 將最佳的模型發佈至服務工作區

* **02-turbofan\_deploy\_model.ipynb：** 此筆記本會採用在上一個筆記本中所建立的模型，並用它來建立已準備好部署至 Azure IoT Edge 裝置的容器映像。

  1. 為模型建立評分指令碼
  2. 建立並發佈映像
  3. 在 Azure 容器執行個體上將該映像部署為 Web 服務
  4. 使用 Web 服務來驗證模型及映像是否能如預期般運作

此文章中的步驟通常是由資料科學家執行的。

## <a name="set-up-azure-notebooks"></a>設定 Azure Notebooks

我們會使用 Azure Notebooks 來裝載兩個 Jupyter 筆記本和支援檔案。 在這裡，我們會建立並設定 Azure Notebooks 專案。 如果您尚未用過 Jupyter 和/或 Azure Notebooks，以下是一些入門的文件：

* **快速入門：** [建立及共用筆記本](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **教學課程：** [使用 Python 建立和執行 Jupyter Notebook](../notebooks/tutorial-create-run-jupyter-notebook.md)

和之前的開發人員虛擬機器相同，使用 Azure 筆記本將能確保練習環境的一致性。

> [!NOTE]
> 設定之後，便可以從任何電腦存取 Azure Notebooks 服務。 您在設定期間應使用開發虛擬機器，其具備您所需的所有檔案。

### <a name="create-an-azure-notebooks-account"></a>建立 Azure Notebooks 帳戶

Azure Notebooks 帳戶是獨立於 Azure 訂用帳戶。 若要使用 Azure Notebooks，您必須建立一個帳戶。

1. 瀏覽至 [Azure Notebooks](https://notebooks.azure.com) \(英文\)。

2. 按一下位於頁面右上角的 [Sign In]  \(登入\)。

3. 使用您的公司或學校帳戶 (Azure Active Directory) 或個人帳戶 (Microsoft 帳戶) 來登入。

4. 如果您從未使用過 Azure Notebooks，系統將會提示您為 Azure Notebooks 應用程式授與權限。

5. 建立適用於 Azure Notebooks 的使用者識別碼。

### <a name="upload-jupyter-notebooks-files"></a>上傳 Jupyter 筆記本檔案

在此步驟中，我們將會建立新的 Azure Notebooks 專案，並將檔案上傳至該專案。 具體來說，我們要上傳的檔案是：

* **01-turbofan\_regression.ipynb**：此 Jupyter 筆記本檔案能逐步引導您完成下載由來自 Azure 儲存體帳戶的裝置載入器所產生的資料，探索並準備用來將分類器定型的資料，對模型進行定型，使用在 Test\_FD003.txt 檔案中所找到的測試資料集測試資料，最後將分類器模型儲存在 Machine Learning 服務工作區中的程序。

* **02-turbofan\_deploy\_model.ipynb：** 此 Jupyter 筆記本能引導您完成使用儲存在 Machine Learning 服務工作區中的分類器模型來產生容器映像的程序。 建立映像之後，此筆記本會引導您完成將映像部署為 Web 服務的程序，使您可以確認其能如預期運作。 已驗證的映像將會在此教學課程的[建立和部署自訂 IoT Edge 模組](tutorial-machine-learning-edge-06-custom-modules.md)部分中被部署至我們的 Edge 裝置。

* **Test\_FD003.txt：** 此檔案包含我們將在驗證已定型分類器時作為測試集使用的資料。 基於簡化範例的因素，我們會選擇使用針對原始競賽所提供的測試資料作為測試集。

* **RUL\_FD003.txt：** 此檔案包含 Test\_FD003.txt 檔案中每個裝置之最後一個週期的 RUL。 請參閱 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 中的 **readme.txt** 和 **Damage Propagation Modeling.pdf** 檔案，以取得該資料的詳細說明。

* **Utils.py：** 包含用來處理資料的 Python 公用程式函式。 第一個筆記本包含這些函式的詳細說明。

* **README.md：** 描述筆記本用途的讀我檔案。

建立新專案並將檔案上傳至您的筆記本。

1. 從頂端功能表列選取 [我的專案]  。

1. 選取 [+ 新增專案]  。 提供名稱和識別碼。 專案不需要被設定為公用或是具備讀我檔案。

1. 選取 [上傳]  ，然後選擇 [從電腦]  。

1. 選取 [選擇檔案]  。

1. 瀏覽至 **C:\source\IoTEdgeAndMlSample\AzureNotebooks**。 選取所有檔案，然後按一下 [開啟]  。

1. 選取 [上傳]  以開始上傳，然後在程序完成時選取 [完成]  。

## <a name="run-azure-notebooks"></a>執行 Azure Notebooks

建立專案之後，請執行 **01-turbofan\_regression.ipynb** 筆記本。

1. 從渦輪風扇專案頁面，選取 **01-turbofan\_regression.ipynb**。

    ![選取要執行的第一個筆記本](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. 出現提示時，請從對話方塊選擇 [Python 3.6 核心]，然後選取 [設定核心]  。

3. 如果該筆記本被列為 [不受信任]  ，請按一下位於筆記本右上方的 [不受信任]  小工具。 出現對話方塊時，請選取 [信任]  。

4. 依照筆記本中的指示進行操作。

    * `Ctrl + Enter` 會執行資料格。
    * `Shift + Enter` 會執行資料格並瀏覽至下一個資料格。
    * 執行資料格時，該資料格將會顯示被方括號括住的星號，如 **[\*]** 。 完成時，該星號將會被數字取代，且下方可能會出現相關的輸出。 由於資料格通常會以先前資料格的工作為基礎來建置，因此一次只能執行一個資料格。

5. 當您完成執行 **01-turbofan\_regression.ipynb** 筆記本之後，請返回專案頁面。

6. 開啟 **02-turbofan\_deploy\_model.ipynb**，並重複此節中的步驟來執行第二個筆記本。

## <a name="next-steps"></a>後續步驟

在此文章中，我們已使用在 Azure Notebooks 中執行的兩個 Jupyter 筆記本，來使用渦輪風扇裝置中的檔案對剩餘使用年限 (RUL) 分類器進行定型、將分類器儲存為模型、建立容器映像，以及將映像測試及部署為 Web 服務。

請前往下一篇文章以建立 IoT Edge 裝置。

> [!div class="nextstepaction"]
> [設定 IoT Edge 裝置](tutorial-machine-learning-edge-05-configure-edge-device.md)
