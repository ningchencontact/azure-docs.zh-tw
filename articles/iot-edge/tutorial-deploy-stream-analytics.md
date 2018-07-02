---
title: 教學課程 - 將 ASA 作業部署至 Azure IoT Edge 裝置 | Microsoft Docs
description: 將 Azure 串流分析作為模組部署至 IoT Edge 裝置
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: ad2895a457a20632823260f2429ac95fad82089c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060190"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>教學課程：將 Azure 串流分析部署為 IoT Edge 模組 - 預覽

許多 IoT 解決方案皆使用分析服務，來深入了解資料從 IoT 裝置送達雲端時的相關情形。 透過 Azure IoT Edge，您可以採取 [Azure 串流分析][azure-stream]邏輯並將其運用於裝置本身。 藉由處理 Edge 上的遙測資料流，您將可減少上傳的資料量，並縮短將見解付諸行動的所需時間。

Azure IoT Edge 和 Azure 串流分析可相互整合，讓您能夠直接在 Azure 入口網站中建立 Azure 串流分析作業，然後將其部署為 IoT Edge 模組，而無需額外的程式碼。  

Azure 串流分析針對雲端中或 IoT Edge 上的資料，提供了豐富結構化的查詢語法。 如需 IoT Edge 上串流分析的詳細資訊，請參閱 [Azure 串流分析文件](../stream-analytics/stream-analytics-edge.md)。

本教學課程中的串流分析模組會計算 30 秒滾動時段的平均溫度。 當平均溫度達到 70 度時，模組就會傳送警示，提醒裝置採取因應動作。 在此案例中，該動作為重設模擬溫度感應器。 在生產環境中，您可能會使用此功能用來關閉機器，或在溫度達到危險程度時採取預防措施。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure 串流分析作業以在邊緣上處理資料。
> * 將新的 Azure 串流分析作業與其他 IoT Edge 模組連結。
> * 從 Azure 入口網站將 Azure 串流分析作業部署至 IoT Edge 裝置。

>[!NOTE]
>IoT Edge 上的 Azure 串流分析模組目前為[公開預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先決條件

* IoT 中樞
* 您在 [Windows][lnk-quickstart-win] 或 [Linux][lnk-quickstart-lin] 的快速入門中建立和設定的 IoT Edge 裝置。 

## <a name="create-an-azure-stream-analytics-job"></a>建立 Azure 串流分析作業

在本節中，您可以透過建立 Azure 串流分析作業，從 IoT 中樞取用資料、查詢從您裝置送出的遙測資料，然後將結果轉送至 Azure Blob 儲存體容器。 如需詳細資訊，請參閱[串流分析文件][azure-stream]的＜概觀＞一節。 

### <a name="create-a-storage-account"></a>建立儲存體帳戶

Azure 串流分析作業必須要有 Azure 儲存體帳戶，才能作為作業輸出的端點。 本節中的範例使用 Blob 儲存體類型。 如需詳細資訊，請參閱 [Azure 儲存體文件][azure-storage]的＜Blob＞一節。

1. 在 Azure 入口網站中，移至 [建立資源]，在搜尋方塊中輸入**儲存體帳戶**，然後選取 [儲存體帳戶 - Blob、檔案、資料表、佇列]。

2. 在 [建立儲存體帳戶] 窗格中，輸入您的儲存體帳戶名稱，選取儲存 IoT 中樞的同個位置，再選取 IoT 中樞所在的相同資源群組，然後選取 [建立]。 記下名稱以在稍後使用。

    ![建立儲存體帳戶][1]


### <a name="create-a-stream-analytics-job"></a>建立串流分析作業

1. 在 Azure 入口網站中，移至 [建立資源] > [物聯網]，然後選取 [串流分析工作]。

2. 在 [新增串流分析作業] 窗格中，執行下列步驟：

   1. 在 [工作名稱] 方塊中，輸入作業名稱。
   
   2. 使用與您的 IoT 中樞相同的 [資源群組] 和 [位置]。 

      > [!NOTE]
      > 目前，在「美國西部 2」區域中並不支援 IoT Edge 上的 Azure 串流分析作業。 

   3. 在 [主控環境] 下，選取 [Edge]。
    
3. 選取 [建立] 。

4. 在已建立的工作中，從 [作業拓撲] 下方開啟 [輸入]。

   ![Azure 串流分析輸入](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. 選取 [新增資料流輸入]，然後選取 [Edge 中樞]。

6. 在 [新增輸入] 窗格中，輸入 [溫度] 作為輸入的別名。 

7. 選取 [ **儲存**]。

8. 在 [作業拓撲] 之下，開啟 [輸出]。

   ![Azure 串流分析輸出](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. 選取 [新增]，然後選取 [Edge 中樞]。

10. 在 [新增輸出] 窗格中，輸入 [警示] 作為輸出別名。 

11. 選取 [ **儲存**]。

12. 在 [作業拓撲] 下選取 [查詢]，並以下列查詢取代預設文字，以建立平均機器溫度在 30 秒的時間範圍內達到 70 度的警示：

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

13. 選取 [ **儲存**]。

14. 在 [設定] 下，選取 [IoT Edge 設定]。

15. 從下拉式功能表中選取您的 [儲存體帳戶]。

16. 針對 [容器] 欄位選取 [新建]，並提供儲存體容器的名稱。 

17. 選取 [ **儲存**]。 


## <a name="deploy-the-job"></a>部署作業

現在您已經準備好要在 IoT Edge 裝置上部署 Azure 串流分析作業。

1. 在 Azure 入口網站中，從您的 IoT 中樞內移至 [IoT Edge]，然後開啟 IoT Edge 裝置的詳細資料頁面。

2. 選取 [設定模組]。  

   如果您先前在此裝置上部署過 tempSensor 模組，它可能會自動填入。 如果沒有，請執行下列步驟以新增模組：

   1. 按一下 [新增]，然後選取 [IoT Edge 模組]。
   2. 針對名稱，輸入 **tempSensor**。
   3. 針對映像 URI，輸入 **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**。 
   4. 其他設定保留不變。
   5. 選取 [ **儲存**]。

3. 執行下列步驟，以新增 Azure 串流分析 Edge 作業：

   1. 按一下 [新增]，然後選取 [Azure 串流分析模組]。
   2. 選取訂用帳戶與您建立的 Azure 串流分析 Edge 作業。 
   3. 選取 [ **儲存**]。

4. 選取 [下一步] 。

5. 將 [路由] 中的預設值取代為下列程式碼。 以您的 Azure 串流分析模組的名稱更新 _{moduleName}_。 模組的名稱應與建立該模組的作業相同。 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

6. 選取 [下一步] 。

7. 在 [檢閱部署] 步驟中，選取 [提交]。

8. 返回裝置的詳細資料頁面，然後選取 [重新整理]。  

    您應該會看到新的串流分析模組正在與 IoT Edge 代理程式模組和 IoT Edge 中樞一起執行。

    ![模組輸出][7]

## <a name="view-data"></a>檢視資料

現在您可以移至您的 IoT Edge 裝置，確認 Azure 串流分析模組和 tempSensor 模組之間的互動。

1. 檢查是否所有模組皆在 Docker 中執行：

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
2. 檢視所有系統記錄和計量資料。 使用串流分析模組名稱：

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

您應該能夠看到機器的溫度逐漸上升，直到在 30 秒內到達 70 度。 接著串流分析模組會觸發程序重設，且機器溫度降回為 21 度。 

   ![Docker 記錄][9]

## <a name="clean-up-resources"></a>清除資源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果您將繼續閱讀下一篇建議的文章，則可以保留您已建立的資源和設定，並加以重複使用。

否則，您可以刪除在本文中建立的本機組態和 Azure 資源，以避免產生費用。 

> [!IMPORTANT]
> 刪除 Azure 資源和資源群組是無法回復的動作。 一旦刪除，資源群組和其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞本身即可，而不要刪除資源群組。
>

若只要刪除 IoT 中樞，請使用中樞名稱和資源群組名稱執行下列命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要依名稱刪除整個資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [資源群組]。

2. 在 [依名稱篩選...] 文字方塊中，輸入包含 IoT 中樞的資源群組名稱。 

3. 在結果清單中的資源群組右側，按一下 **...**，然後按一下 [刪除資源群組]。

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. 系統將會要求您確認是否刪除資源。 再次輸入您的資源群組名稱進行確認，然後按一下 [刪除]。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您設定了 Azure 串流分析作業，以分析來自 IoT Edge 裝置的資料。 接著，您載入將此 Azure 串流分析模組載入至 IoT Edge 裝置，以處理及因應本機的溫度上升，以及將彙總的資料流傳送至雲端。 若要進一步了解 Azure IoT Edge 可如何為您的企業建立解決方案，請繼續閱讀其他教學課程。

> [!div class="nextstepaction"] 
> [將 Azure Machine Learning 模型部署為模組][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

