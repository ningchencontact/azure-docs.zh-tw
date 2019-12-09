---
title: 教學課程 - 在 Azure IoT Central 中定義新的 Azure IoT Edge 裝置類型
description: 本教學課程將為建置者說明如何在 Azure IoT Central 應用程式中建立新的 Azure IoT Edge 裝置。 您會定義該類型的遙測、狀態、屬性和命令。
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 97bfd2b1e8b571f44c0b782459567f5677dd36a7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702798"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>教學課程：在 Azure IoT Central 應用程式中定義新的 Azure IoT Edge 裝置類型 (預覽功能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教學課程將為建置者說明如何使用裝置範本在 Azure IoT Central 應用程式中定義新的 Azure IoT Edge 裝置類型。 

如需概觀，請參閱[什麼是 IoT Central (預覽功能)？](overview-iot-central.md) 

IoT Edge 是由三個元件組成：
* **IoT Edge 模組**是執行 Azure 服務、夥伴服務或自有程式碼的容器。 這類模組會部署到 IoT Edge 裝置，並在這些裝置本機上執行。
* **IoT Edge 執行階段**會在每個 IoT Edge 裝置上執行，並管理部署到每個裝置的模組。
* **雲端式介面**可讓您在遠端監視及管理 IoT Edge 裝置。 IoT Central 是雲端介面。

**Azure IoT Edge** 裝置可以作為閘道裝置，具有連線到 IoT Edge 裝置的下游裝置。 本教學課程會分享有關下游裝置連線模式的詳細資訊。

**裝置範本**會定義裝置和 IoT Edge 模組的功能。 功能包括了模組傳送的遙測資料、模組屬性，以及模組回應的命令。

在本教學課程中，您會建立環境感應器裝置範本。 環境感應器裝置：

* 傳送遙測資料，例如溫度。
* 可寫入的屬性在雲端上有所更新時加以回應 (例如遙測資料傳送間隔)。
* 回應命令，例如重設溫度。

另外，在本教學課程中，您會建立環境閘道裝置範本。 環境閘道裝置：

* 傳送遙測資料，例如溫度。
* 可寫入的屬性在雲端上有所更新時加以回應 (例如遙測資料傳送間隔)。
* 回應命令，例如重設溫度。
* 允許與其他裝置功能模型的關聯性。


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立新 Azure IoT Edge 裝置的裝置範本。
> * 上傳部署資訊清單。
> * 建立功能，包括每個模組的遙測、屬性和命令。
> * 定義模組遙測的視覺效果。
> * 將關聯性新增至下游裝置範本。
> * 發佈您的裝置範本。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須[建立 Azure IoT Central 應用程式](quick-deploy-iot-central.md)。


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>下游裝置與閘道和模組的關聯性

下游裝置可以透過 `$edgeHub` 模組連接到 IoT Edge 閘道裝置。 此 IoT Edge 裝置在此案例中會變成透明閘道。

![透明閘道的圖表](./media/tutorial-define-edge-device-type/gateway-transparent.png)

下游裝置也可以透過自訂模組連接到 IoT Edge 閘道裝置。 在下列案例中，下游裝置會透過 Modbus 自訂模組來連接。

![自訂模組連接的圖表](./media/tutorial-define-edge-device-type/gateway-module.png)

下圖顯示透過兩種模組類型 (自訂和 `$edgeHub`) 連接到 IoT Edge 閘道裝置的連線。  

![透過兩個連接模組連接的圖表](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

最後，下游裝置可以透過多個自訂模組連接到 IoT Edge 閘道裝置。 下圖顯示透過 Modbus 自訂模組、BLE 自訂模組和 `$edgeHub` 模組連接的下游裝置。 

![透過多個自訂模組連接的圖表](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>建立範本

身為建置者，您可以在應用程式中建立和編輯 IoT Edge 裝置範本。 發佈裝置範本之後，您可以連接實作裝置範本的實際裝置。

### <a name="select-device-template-type"></a>選取裝置範本類型 

若要將新的裝置範本新增至應用程式，請選取左側窗格上的 [裝置範本]  。

![醒目提示裝置範本的預覽應用程式螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

選取 [+ 新增]  以開始建立新的裝置範本。

![已醒目提示「新增」的 [裝置範本] 頁面幕擷取畫面](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

在 [選取範本類型]  頁面上，選取 [Azure IoT Edge]  ，然後選取 [下一步：  自訂]。

![[選取範本類型] 頁面的螢幕擷取畫面](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>自訂裝置範本

在 IoT Edge 中，您可以透過模組形式來部署和管理商務邏輯。 IoT Edge 模組是 IoT Edge 管理的最小計算單位，可以包含 Azure 服務 (例如 Azure 串流分析) 或您自己的解決方案特定程式碼。 若要了解如何開發、部署和維護模組，請參閱 [IoT Edge 模組](../../iot-edge/iot-edge-modules.md)。

概括而言，部署資訊清單是以其所需屬性設定的模組對應項清單。 部署資訊清單會告知 IoT Edge 裝置 (或裝置群組) 要安裝哪些模組，以及如何設定。 部署資訊清單包含每個模組對應項的所需屬性。 IoT Edge 裝置會回報每個模組的報告屬性。

請使用 Visual Studio Code 建立部署資訊清單， 若要深入了解，請參閱[適用於 Visual Studio Code 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。

以下是含有一個模組的基本部署資訊清單，作為供本教學課程使用的範例。 複製下列 JSON 並儲存為 .json 檔案。 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

#### <a name="upload-an-iot-edge-deployment-manifest"></a>上傳 IoT Edge 部署資訊清單

在 [自訂裝置]  頁面上的 [上傳 Azure IoT Edge 部署資訊清單]  底下，選取 [瀏覽]  。 

![已醒目提示 [瀏覽] 的 [自訂裝置] 頁面螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

如果您打算建立 IoT Edge 閘道裝置範本，請務必選取 [具有下游裝置的閘道裝置]  。

![[自訂裝置] 頁面的螢幕擷取畫面，其中已醒目提示具有下游裝置的閘道裝置](./media/tutorial-define-edge-device-type/gateway-upload.png)

在檔案選取對話方塊中，請選取部署資訊清單檔，然後選取 [開啟]  。

IoT Edge 會根據結構描述來驗證部署資訊清單檔。 如果驗證成功，請選取 [檢閱]  。

![[自訂裝置] 頁面的螢幕擷取畫面，其中已醒目提示部署資訊清單和檢閱](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

下列流程圖說明 IoT Central 中的部署資訊清單生命週期。

![部署資訊清單生命週期的流程圖](./media/tutorial-define-edge-device-type/dmflow.png)

接下來，您會看到 [檢閱] 頁面，其中會列出部署資訊清單的詳細資料。 此頁面會顯示部署資訊清單中的模組清單。 在本教學課程中，您會看到其中已列出 `SimulatedTemperatureSensor` 模組。 選取 [建立]  。

![[檢閱] 頁面的螢幕擷取畫面，其中已醒目提示 [模組] 和 [建立]](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

如果您已選取閘道裝置，您會看到下列檢閱頁面。

![[檢閱] 頁面的螢幕擷取畫面，其中已醒目提示 Azure IoT Edge 閘道](./media/tutorial-define-edge-device-type/gateway-review.png)


您會以模組功能模型來建立裝置範本。 在本教學課程中，您會以 `SimulatedTemperatureSensor` 模組功能模型來建立裝置範本。 

請將裝置範本的標題變更為**環境感應器裝置範本**。

![裝置範本的螢幕擷取畫面，其中已醒目提示更新的標題](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

在 IoT Edge 裝置中，IoT 隨插即用模型如下所示：
* 每個 IoT Edge 裝置範本都有一個裝置功能模型。
* 部署資訊清單中列出的每個自訂模組，都會產生模組功能模型。
* 系統會在每個模組功能模型和裝置功能模型之間建立關聯性。
* 模組功能模型會實作模組介面。
* 每個模組介面都包含遙測、屬性和命令。

![IoT Edge 模型的圖表](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>將功能加入模組功能模型

以下是 `SimulatedTemperatureSensor` 模組中的範例輸出：
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

您可以將功能新增至 `SimulatedTemperatureSensor` 模組，以反映上述輸出。 

1. 若要管理 `SimulatedTemperatureSensor` 模組功能模型的介面，請選取 [管理]   > [新增功能]  。 

    ![醒目提示 [新增功能] 的環境感應器範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. 新增作為物件類型的機器。
  
    ![環境感應器範本功能頁面的螢幕擷取畫面，其中已醒目提示結構描述](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. 選取 [定義]  。 在顯示的對話方塊中，將物件名稱變更為 [機器]  。 建立溫度和壓力屬性，然後選取 [套用]  。
  
    ![醒目提示各種選項的屬性對話方塊螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. 新增**環境**作為物件類型。

1. 選取 [定義]  。 在顯示的對話方塊中，將物件名稱變更為**環境**。 建立溫度和濕度屬性，然後選取 [套用]  。
  
    ![醒目提示各種選項的屬性對話方塊螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. 將 `timeCreated` 新增為 `DateTime` 類型，然後選取 [儲存]  。
  
    ![醒目提示 [儲存] 的環境感應器範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>新增關聯性

如果您選取 IoT Edge 裝置作為閘道裝置，對於要連接到閘道裝置的裝置，則可以將下游關聯性新增到裝置功能模型上。
  
  ![醒目提示 [新增關聯性] 的環境閘道範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

您可以在裝置或模組上新增關聯性。
  
  ![醒目提示裝置和模組層級關聯性的環境閘道範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


您可以選取下游裝置功能模型，或是選取星號。 
  
  ![醒目提示 [目標] 的環境閘道範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  在本教學課程中，請選取星號。 此選項可允許任何下游關聯性。 然後選取 [儲存]  。

  ![醒目提示 [目標] 的環境閘道範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>新增雲端屬性

裝置範本可包含雲端屬性。 雲端屬性僅存在於 IoT Central 應用程式中，且一律不會傳送至或接收自裝置。

1. 選取 [雲端屬性]   > [+ 新增雲端屬性]  。 請使用下表中的資訊，將雲端屬性新增至您的裝置範本。

    | 顯示名稱      | 語意類型 | 結構描述 |
    | ----------------- | ------------- | ------ |
    | 上次維修日期 | None          | Date   |
    | 客戶名稱     | None          | 字串 |

2. 選取 [儲存]  。

  
    ![醒目提示 [儲存] 的環境感應器範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>新增自訂

若要修改介面，或將 IoT Central 特有的功能新增至不需要您控制裝置功能模型版本的功能時，請使用自訂。 當功能模型處於草稿或已發佈狀態時，您可以自訂欄位。 您只能自訂不會中斷介面相容性的欄位。 例如，您可以：

- 自訂功能的顯示名稱和單位。
- 新增在圖表上顯示值時所要使用的預設色彩。
- 指定屬性的初始、最小和最大值。

您無法自訂功能名稱或功能類型。

完成自訂之後，請選取 [儲存]  。
  
![環境感應器範本自訂頁面的螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>建立檢視

身為建置者，您可以自訂應用程式，以對操作員顯示環境感應器裝置的相關資訊。 您的自訂將使操作員能夠管理已連線至應用程式的環境感應器裝置。 您可以建立兩種類型的檢視，讓操作員用來與裝置互動：

* 用於檢視及編輯裝置和雲端屬性的表單。
* 用來將裝置視覺化的儀表板。

### <a name="configure-a-view-to-visualize-devices"></a>設定將裝置視覺化的檢視

裝置儀表板可讓操作員使用圖表和計量將裝置視覺化。 身為建置者，您可以定義要在裝置儀表板上顯示哪些資訊。 您可以為裝置定義多個儀表板。 若要建立將環境感應器遙測資料視覺化的儀表板，請選取 [檢視]   > [將裝置視覺化]  ：

  
![環境感應器範本檢視頁面的螢幕擷取畫面，其中已醒目提示 [將裝置視覺化]](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


環境遙測和機器遙測都是複雜物件。 若要建立圖表：

1. 拖曳 [環境遙測]  ，然後選取 [折線圖]  。 
  
   ![醒目提示環境遙測和折線圖的環境感應器範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. 選取設定圖示。 選取 [溫度]  和 [濕度]  ，將資料視覺化，然後選取 [更新設定]  按鈕。 
  
   ![醒目提示各種選項的環境感應器範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. 選取 [儲存]  。

您可以新增更多顯示其他屬性或遙測值的圖格。 您也可以新增靜態文字、連結和影像。 若要移動儀表板上的圖格或調整其大小，請將滑鼠指標移至圖格上方，然後將圖格拖曳到新位置或調整其大小。
  
![環境感應器範本儀表板檢視的螢幕擷取畫面](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>新增裝置表單

裝置表單可讓操作員編輯可寫入的裝置屬性和雲端屬性。 身為建置者，您可以定義多個表單，並選擇要在每個表單上顯示的裝置和雲端屬性。 您也可以在表單上顯示唯讀裝置屬性。

若要建立表單以檢視和編輯環境感應器屬性：

1. 在**環境感應器範本**中，移至 [檢視]  。 選取 [編輯裝置和雲端資料]  圖格來新增檢視。
  
   ![環境感應器範本檢視頁面的螢幕擷取畫面，其中已醒目提示編輯裝置和雲端資料](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. 輸入表單名稱**環境感應器屬性**。

1. 將 [客戶名稱]  和 [上次維修日期]  雲端屬性拖曳到表單上的現有區段。
  
   ![醒目提示各種選項的環境感應器範本檢視頁面螢幕擷取畫面](./media/tutorial-define-edge-device-type/views-properties.png)

1. 選取 [儲存]  。

## <a name="publish-a-device-template"></a>發佈裝置範本

您必須先發佈裝置範本，才能建立模擬的環境感應器或連接實際的環境感應器。

若要發佈裝置範本：

1. 從 [裝置範本]  頁面移至您的裝置範本。

2. 選取 [發佈]  。
  
    ![醒目提示 [發佈] 的環境感應器範本螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. 在 [發佈裝置範本]  對話方塊中，選擇 [發佈]  。
  
    ![醒目提示 [發佈] 的 [發佈裝置範本] 對話方塊螢幕擷取畫面](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

裝置範本發佈後，將會顯示在 [裝置]  頁面上，並且對操作員顯示。 在已發佈的裝置範本中，您無法直接編輯裝置功能模型而不建立新版本。 不過，您可以直接在已發佈的裝置範本中更新雲端屬性、自訂和檢視。 這些更新不會導致新版本建立。 進行任何變更之後，請選取 [發佈]  ，將這些變更推送給您的操作員。
  
![已發佈範本的裝置範本清單螢幕擷取畫面](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

* 建立新的 Edge 作為分葉裝置範本。
* 從上傳的部署資訊清單產生模組。
* 新增複雜類型的遙測和屬性。
* 建立雲端屬性。
* 建立自訂。
* 定義裝置遙測的視覺效果。
* 發佈您的 Edge 裝置範本。

現在，您已在 Azure IoT Central 應用程式中建立裝置範本，接下來您可以執行下列動作：

> [!div class="nextstepaction"]
> [連接裝置](./tutorial-connect-pnp-device.md)
