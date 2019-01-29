---
title: 使用 Azure Data Box Edge 轉換資料 | Microsoft Docs
description: 了解如何設定 Data Box Edge 上的計算角色，並使用它在資料傳送至 Azure 之前先轉換資料。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c52c311f1e1cd1335ea5797eadacd0bc89e1b36c
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402110"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>教學課程：使用 Azure Data Box Edge 轉換資料 (預覽)

本教學課程說明如何在 Azure Data Box Edge 裝置上設定計算角色。 當您設定計算角色之後，Data Box Edge 就能先轉換資料，然後再將資料傳送至 Azure。

此程序大約需要 30 至 45 分鐘才能完成。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure IoT 中樞資源
> * 設定計算角色
> * 新增計算模組
> * 確認資料轉換和傳輸

> [!IMPORTANT]
> Data Box Edge 目前處於預覽狀態。 在訂購及部署此解決方案之前，請先檢閱 [Azure 預覽版使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="prerequisites"></a>必要條件

在 Data Box Edge 裝置上設定計算角色之前，請確定：

* 您已依照[連線、設定及啟用 Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md) 中的說明來啟動 Data Box Edge 裝置。


## <a name="create-an-iot-hub-resource"></a>建立 IoT 中樞資源

在 Data Box Edge 上設定計算角色之前，您必須先建立 IoT 中樞資源。

如需詳細指示，請移至[建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub)。 請使用您用於 Data Box Edge 資源的相同訂用帳戶和資源群組。

![建立 IoT 中樞資源](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

如果尚未設定 Edge 計算角色，則適用下列注意事項：

- IoT 中樞資源沒有任何 Azure IoT 裝置或 Azure IoT Edge 裝置。
- 您無法建立 Edge 本機共用。 當您新增共用時，為 Edge 計算建立本機共用的選項不會啟用。


## <a name="set-up-compute-role"></a>設定計算角色

在 Edge 裝置上設定 Edge 計算角色時，其會建立兩個裝置：一個 IoT 裝置和一個 IoT Edge 裝置。 這兩個裝置都可以在 IoT 中樞資源中檢視。

若要在裝置上設定計算角色，請執行下列步驟：

1. 移至 Data Box Edge 資源、選取 [概觀]，然後選取 [設定計算角色]。 

    ![左側窗格中的 [概觀] 連結](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    (選擇性) 您可以移至 [模組]，然後選取 [設定計算]。

    ![[模組] 和 [設定計算] 連結](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. 在下拉式清單中，選取您在上一個步驟中建立的 **IoT 中樞資源**。  
    目前只有 Linux 平台適用於您的 IoT Edge 裝置。 
    
1. 按一下頁面底部的 [新增] 。

    ![[建立] 按鈕](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    建立計算角色需要幾分鐘的時間。 由於此版本中的 Bug，即使在計算角色建立後，畫面仍不會重新整理。 若要確認已設定 Edge 計算角色，請移至 [模組]。  

    ![[設定 Edge 計算] 裝置清單](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. 再次移至 [概觀]。  
    此畫面已更新並指出計算角色已設定。

    ![設定計算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. 在您建立 Edge 計算角色時所使用的 IoT 中樞，移至 [IoT 裝置]。  
    IoT 裝置現在已啟用。 

    ![[IoT 裝置] 頁面](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. 在左側窗格中，選取 [IoT Edge]。  
    IoT Edge 裝置也會啟用。

    ![設定計算角色](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. 選取並按一下 IoT Edge 裝置。  
    Edge 代理程式會在此 IoT Edge 裝置上執行。 

    ![[裝置詳細資料] 頁面](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    此 Edge 裝置上沒有任何自訂模組，但您現在可以新增自訂模組。 若要了解如何建立自訂模組，請移至[為您的 Data Box Edge 裝置開發 C# 模組](data-box-edge-create-iot-edge-module.md)。


## <a name="add-a-custom-module"></a>新增自訂模組

在本節中，您會將自訂模組新增至您在[為您的 Data Box Edge 開發 C# 模組](data-box-edge-create-iot-edge-module.md)中建立的 IoT Edge 裝置。 

在下列程序所使用的範例中，自訂模組會從 Edge 裝置上的本機共用中取用檔案，並將其移至裝置上的雲端共用。 接著，雲端共用會將檔案推送至與該雲端共用相關聯的 Azure 儲存體帳戶。 

1. 執行下列步驟，在 Edge 裝置上新增本機共用：

    a. 在您的 Data Box Edge資源中，移至 [共用]。 
    
    b. 選取 [新增共用]，然後提供共用名稱並選取共用類型。 
    
    c. 若要建立本機共用，請選取 [設定為 Edge 本機共用] 核取方塊。 
    
    d. 選取 [新建] 或 [使用現有的]，然後選取 [建立]。

    ![新增自訂模組](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    如果您已建立本機 NFS 共用，請使用下列遠端同步 (rsync) 命令選項來將檔案複製到共用上：

    `rsync --inplace <source file path> < destination file path>`

    如需 rsync 命令的詳細資訊，請移至 [Rsync 文件](https://www.computerhope.com/unix/rsync.htm) \(英文\)。 

    本機共用即會建立，而您將會收到成功建立通知。 共用清單可能會更新，但您必須等到共用建立完成。
    
1. 移至共用清單。 

    ![新增自訂模組](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. 若要檢視新建立之本機共用的屬性，請選取它。 

1. 在 [用於 Edge 計算模組的本機掛接點] 方塊中，複製對應到此共用的值。  
    當您部署模組時，將會使用這個本機掛接點。

    ![[用於 Edge 計算模組的本機掛接點] 方塊](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. 在已建立於 Data Box Edge 裝置上的現有雲端共用上，於 [用於 Edge 計算模組的本機掛接點] 方塊中，針對此雲端共用複製 Edge 計算模組的本機掛接點。  
    當您部署模組時，將會使用這個本機掛接點。

    ![新增自訂模組](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. 若要將自訂模組新增至 IoT Edge 裝置，請移至您的 IoT 中樞資源，然後移至 [IoT Edge 裝置]。 

1. 選取該裝置，然後在 [裝置詳細資料] 下方，選取 [設定模組]。 

    ![[設定模組] 連結](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. 在 [新增模組] 下方，執行下列步驟：

    a. 針對自訂模組的容器登錄設定，輸入名稱、位址、使用者名稱和密碼。  
    名稱、位址和列出的認證會用來擷取具有相符 URL 的模組。 若要部署此模組，請在 [部署模組] 下方選取 [IoT Edge 模組]。 此 IoT Edge 模組是一個 Docker 容器，可供您部署至與您 Data Box Edge 裝置相關聯的 IoT Edge 裝置。

    ![[設定模組] 頁面](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. 輸入您的模組名稱和對應容器映像的映像 URI，來指定 IoT Edge 自訂模組的設定。 
    
    ![[IoT Edge 自訂模組] 頁面](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. 在 [容器建立選項] 方塊中，輸入您在先前步驟中針對雲端和本機共用所複製之 Edge 模組的本機掛接點。
    > [!IMPORTANT]
    > 使用複製的路徑；請勿建立新路徑。 本機掛接點會對應至您在[使用自訂程式碼更新模組](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)時指定於模組中的對應 **InputFolderPath** 和 **OutputFolderPath**。 
    
    在 [容器建立選項] 方塊中，您可以貼上下列範例： 
    
    ```
    {
        "HostConfig": {
        "Binds": [
        "/home/hcsshares/mysmblocalshare:/home/LocalShare",
        "/home/hcsshares/mysmbshare1:/home/CloudShare"
        ]
        }
    }
    ```

    此外，請在此處提供適用於模組的任何環境變數。

    ![[容器建立選項] 方塊](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. 視需要設定進階 Edge 執行階段設定，然後按 [下一步]。

    ![新增自訂模組](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  在 [指定路由] 下方，設定模組之間的路由。  
    在此範例中，輸入要將資料推送至雲端共用的本機共用名稱。

    您可以使用下列路由字串來取代 *route*：       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![[指定路由] 區段](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. 選取 [下一步] 。 

1.  在 [檢閱部署] 下方檢閱所有設定，然後選取 [提交] 以提交模組進行部署。

    ![[設定模組] 頁面](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    這個動作會啟動模組部署，如下圖所示：

    ![新增自訂模組](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>確認資料轉換和傳輸

最後一個步驟是確定模組已如預期般連線並執行。 在 IoT 中樞資源中，模組應處於為您的 IoT Edge 裝置執行中的執行階段狀態。

![確認資料轉換](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
若要確認模組正在執行中，請執行下列步驟：

1. 選取模組，然後檢視 [模組身分識別對應項]。  
    Edge 裝置和模組的用戶端狀態應顯示為 [已連線]。

    ![確認資料轉換](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    模組開始執行後，也會顯示在您 Data Box Edge 資源中的 Edge 模組清單下方。 您新增的模組處於「執行中」的執行階段狀態。

    ![確認資料轉換](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  在 [檔案總管] 中，連線到您先前建立的本機和雲端共用。

    ![確認資料轉換](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  將資料新增至本機共用。

    ![確認資料轉換](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    資料會移至雲端共用。

    ![確認資料轉換](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    然後將資料從雲端共用推送至儲存體帳戶。 若要檢視資料，請移至 [儲存體總管]。

    ![確認資料轉換](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
您已完成驗證流程。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立 IoT 中樞資源
> * 設定計算角色
> * 新增計算模組
> * 確認資料轉換和傳輸

若要了解如何管理您的 Data Box Edge 裝置，請參閱：

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理 Data Box Edge](https://aka.ms/dbg-docs)


