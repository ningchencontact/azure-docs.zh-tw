---
title: 在 Azure IoT Central 中定義新的 Azure IoT Edge 裝置類型 | Microsoft Docs
description: 本教學課程將為建置者說明如何在 Azure IoT Central 應用程式中建立新的 Azure IoT Edge 裝置。 您會定義該類型的遙測、狀態、屬性和命令。
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892024"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>教學課程：在 Azure IoT Central 應用程式中定義新的 Azure IoT Edge 裝置類型 (預覽功能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教學課程將為建置者說明如何使用裝置範本在 Azure IoT Central 應用程式中定義新的 Azure IoT Edge 裝置類型。 

若要深入了解 Azure IoT Edge，請[參閱這篇文章](overview-iot-central.md)。 

Azure IoT Edge 是由三個元件組成：
* **IoT Edge 模組**是執行 Azure 服務、第三方服務或自有程式碼的容器。 這類模組會部署到 IoT Edge 裝置，並在這些裝置本機上執行。
* **IoT Edge 執行階段**會在每個 IoT Edge 裝置上執行，並管理部署到每個裝置的模組。
* **雲端式介面**可讓您在遠端監視及管理 IoT Edge 裝置。 IoT Central 會是雲端介面。

**Azure IoT Edge** 裝置可以作為閘道裝置，具有連線到 Azure IoT Edge 裝置的下游裝置。 在本篇教學課程中，會探討下游裝置的連線模式。

**裝置範本**會定義裝置和 IoT Edge 模組的功能。 功能包括了模組傳送的遙測資料、模組屬性，以及模組回應的命令。

在本教學課程中，您會建立**環境感應器**裝置範本。 環境感應器裝置：

* 傳送遙測資料，例如溫度。
* 可寫入的屬性在雲端上有所更新時 (例如遙測資料傳送間隔)，加以回應。
* 回應命令，例如重設溫度。

另外，在本教學課程中，會建立**環境閘道**裝置範本。 環境閘道裝置：

* 傳送遙測資料，例如溫度。
* 可寫入的屬性在雲端上有所更新時 (例如遙測資料傳送間隔)，加以回應。
* 回應命令，例如重設溫度。
* 允許與其他裝置功能模型的關聯性


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立新 Azure IoT Edge 裝置的裝置範本。
> * 上傳部署資訊清單。
> * 建立功能，包括每個模組的遙測、屬性和命令
> * 定義模組遙測的視覺效果。
> * 將關聯性新增至下游裝置範本
> * 發佈您的裝置範本。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須要有 Azure IoT 中心應用程式。 按照此快速入門[建立 Azure IoT Central 應用程式](quick-deploy-iot-central.md) (英文)。


## <a name="downstream-device-relationships-with-gateway--modules"></a>下游裝置與閘道和模組的關聯性

下游裝置可以透過 $edgeHub 模組連接到 Azure IoT Edge 閘道裝置。 此 Azure IoT Edge 裝置在此案例中會變成透明閘道

![Central 應用程式頁面](./media/tutorial-define-edge-device-type/gateway-transparent.png)

下游裝置可以透過自訂模組連接到 Azure IoT Edge 閘道裝置。 在下列案例中，下游裝置是透過 Modbus 自訂模組連接，而下游裝置可以透過 $edgeHub 模組連接到 Azure IoT Edge 閘道裝置。

![Central 應用程式頁面](./media/tutorial-define-edge-device-type/gateway-module.png)

下游裝置可以透過自訂模組連接到 Azure IoT Edge 閘道裝置。 在下列案例中，下游裝置會透過 Modbus 自訂模組來連接。 

![Central 應用程式頁面](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

下游裝置可以透過多個自訂模組連接到 Azure IoT Edge 閘道裝置。 在下列案例中，下游裝置是透過 Modbus 自訂模組連接，而 BLE 自訂模組和下游裝置可以透過 $edgeHub 模組連接到 Azure IoT Edge 閘道裝置。 

![Central 應用程式頁面](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>建立範本

身為建置者，您可以在應用程式中建立和編輯 Azure IoT Edge 裝置範本。 發佈裝置範本之後，您可以連接實作裝置範本的實際裝置。

### <a name="select-device-template-type"></a>選取裝置範本類型 

若要將新的裝置範本新增至應用程式，請移至 [裝置範本]  頁面。 若要這樣做，請選取左側窗格上的 [裝置範本]  索引標籤。

![Central 應用程式頁面](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

按一下 [+ 新增]  以開始建立新裝置範本。

![裝置範本 - 新增](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

會將您帶往裝置範本類型選取頁面。 請選取 [Azure IoT Edge]  圖格，然後按一下 [下一步] **：按一下底部的**  按鈕

![裝置範本選取 - Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>自訂裝置範本

Azure IoT Edge 可讓您以模組形式部署和管理商務邏輯。 **Azure IoT Edge 模組**是 IoT Edge 管理的最小計算單位，可以包含 Azure 服務 (例如 Azure 串流分析) 或您自己的解決方案特定程式碼。 若要了解如何開發、部署和維護模組，請參閱 [IoT Edge 模組](../../iot-edge/iot-edge-modules.md)。

概括而言，部署資訊清單是以其所需屬性設定的模組對應項清單。 部署資訊清單會告知 IoT Edge 裝置 (或裝置群組) 要安裝哪些模組，以及如何設定它們。 部署資訊清單包含每個模組對應項的所需屬性。 IoT Edge 裝置會回報每個模組的報告屬性。

請使用 Visual Studio Code 建立部署資訊清單， 按照如何建立[部署資訊清單](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)的文件操作。

以下是含有一個模組的基本部署資訊清單，作為供本教學課程使用的範例。 複製下列 JSON 並儲存為 json 檔案。 

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

**上傳 Azure IoT Edge 部署資訊清單**

按一下 [瀏覽]  按鈕 

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

如果您打算建立 Azure IoT Edge 閘道裝置範本，請務必選取 [Gateway device with downstream devices]\(閘道裝置與下游裝置\)  核取方塊

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

您會看到可供選取檔案的對話方塊。 請選取部署資訊清單檔，然後按一下 [開啟]  按鈕。

會對照結構描述向部署資訊清單檔進行驗證。 驗證成功之後，請按一下 [檢閱]  按鈕

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

以下是 IoT Central 中部署資訊清單生命週期的流程。

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

[檢閱] 頁面會列出部署資訊清單的詳細資料。 部署資訊清單中的模組清單會顯示在 [檢閱] 頁面上。 在本教學課程中，會看到列出了 SimulatedTemperatureSensor 模組。 按一下 [建立]  按鈕。

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

如果您已選取閘道裝置，就會看到此 [檢閱] 頁面

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


會顯示建立新裝置範本的進度環，代表正在 IoT Central 中建立裝置範本。

裝置範本是以模組功能模型所建立。 在本教學課程中，會看到建立了 SimulatedTemperatureSensor 模組功能模型。 

請將裝置範本的標題變更為「環境感應器裝置範本」。

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge 裝置隨插即用的建模方式會按以下步驟完成
* 每個 Azure IoT Edge 裝置範本都有一個**裝置功能模型**
* 部署資訊清單中列出的每個自訂模組，都會產生**模組功能模型**
* 系統會在每個模組功能模型和裝置功能模型之間建立**關聯性**
* 模組功能模型會實作**模組介面**
* 每個模組介面包含
   - 遙測
   - properties
   - 命令

![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**將功能加入模組功能模型**

以下是來自 SimulatedTemperatureSensor 模組的輸出範例
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

請將功能新增至 SimulatedTemperatureSensor 模組，如此會反映上述的 JSON。 

* 按一下 [管理]  SimulatedTemperatureSensor 模組功能模型的介面。 按一下 [新增功能]  。 

    ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* 因為機器是複雜類型，請新增為物件類型
  
    ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    按一下 [定義]  。 在快顯視窗中，將物件名稱變更為機器，並建立屬性溫度、壓力，然後按一下 [套用] 
  
    ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* 因為環境是複雜類型，請新增為物件類型

    按一下 [定義]  。 在快顯視窗中，將物件名稱變更為環境，並建立屬性溫度、濕度，然後按一下 [套用] 
  
    ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* 將 timeCreated 新增為 DateTime 類型，然後按一下 [儲存] 
  
    ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>新增關聯性

如果您選取 Azure IoT Edge 裝置作為閘道裝置，對於將會連接到閘道裝置的裝置，則可以將下游關聯性新增到裝置功能模型上。
  
  ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

可以在裝置或模組新增關聯性。
  
  ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


您可以選取下游裝置功能模型，也可以選取星號。 
  
  ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  本教學課程會選取星號，即表示允許任何的下游關聯性。 按一下 [儲存] 

  ![裝置範本 - Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>新增雲端屬性

裝置範本可包含雲端屬性。 雲端屬性僅存在於 IoT Central 應用程式中，且一律不會傳送至或接收自裝置。

1. 依序選取 [雲端屬性]  和 [+ 新增雲端屬性]  。 請使用下表中的資訊，將雲端屬性新增至您的裝置範本。

    | 顯示名稱      | 語意類型 | 結構描述 |
    | ----------------- | ------------- | ------ |
    | 上次維修日期 | None          | Date   |
    | 客戶名稱     | None          | 字串 |

2. 選取 [儲存]  以儲存變更：

  
    ![雲端屬性 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>新增自訂

當您需要修改介面，或將 IoT Central 特有的功能新增至不需要您控制裝置功能模型版本的功能時，請使用自訂。 當功能模型處於草稿或已發佈狀態時，您可以自訂欄位。 您只能自訂不會中斷介面相容性的欄位。 例如，您可以：

- 自訂功能的顯示名稱和單位。
- 新增在圖表上顯示值時所要使用的預設色彩。
- 指定屬性的初始、最小和最大值。

您無法自訂功能名稱或功能類型。 按一下 [儲存] 
  
![自訂 - Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>建立檢視

身為建置者，您可以自訂應用程式，以對操作員顯示環境感應器裝置的相關資訊。 您的自訂將使操作員能夠管理已連線至應用程式的環境感應器裝置。 您可以建立兩種類型的檢視，讓操作員用來與裝置互動：

* 用於檢視及編輯裝置和雲端屬性的表單。
* 用來將裝置視覺化的儀表板。

### <a name="configure-a-view-to-visualize-devices"></a>設定將裝置視覺化的檢視

裝置儀表板可讓操作員使用圖表和計量將裝置視覺化。 身為建置者，您可以定義要在裝置儀表板上顯示哪些資訊。 您可以為裝置定義多個儀表板。 若要建立將環境感應器遙測資料視覺化的儀表板，請選取 [檢視]  ，然後**將裝置視覺化**：

  
![檢視 - Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


環境遙測和機器遙測是複雜物件，若要建立圖表，請執行下列動作

拖曳 [環境遙測]，然後選取折線圖。 
  
![檢視 - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

按一下 [設定] 圖示，然後選取溫度和濕度，將資料視覺化，然後按一下 [更新設定]  按鈕。 
  
![檢視 - Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

選取 [儲存]  以儲存檢視：

您可以新增更多顯示其他屬性或遙測值的圖格。 您也可以新增靜態文字、連結和影像。 若要移動儀表板上的圖格或調整其大小，請將滑鼠指標移至圖格上方，然後將圖格拖曳到新位置或調整其大小。
  
![檢視 - Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>新增裝置表單

裝置表單可讓操作員編輯可寫入的裝置屬性和雲端屬性。 身為建置者，您可以定義多個表單，並選擇要在每個表單上顯示的裝置和雲端屬性。 您也可以在表單上顯示唯讀裝置屬性。

若要建立表單以檢視和編輯環境感應器屬性：

流覽至**環境感應器**範本中的 [檢視]  。 選取 [編輯裝置和雲端資料]  圖格來新增檢視。
  
![檢視 - Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

輸入表單名稱**環境感應器屬性**。

將 [客戶名稱]  和 [上次維修日期]  雲端屬性拖曳到表單上的現有區段。
  
![檢視 - Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

選取 [儲存]  以儲存檢視。

### <a name="generate-default-views"></a>產生預設檢視

Azure IoT Edge 範本不支援產生預設檢視的功能 

## <a name="publish-device-template"></a>發佈裝置範本

您必須先發佈裝置範本，才能建立模擬的環境感應器或連接實際的環境感應器。

若要發佈裝置範本：

1. 從 [裝置範本]  頁面移至您的裝置範本。

2. 選取 [發佈]  。
  
    ![檢視 - 發佈](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. 在 [發佈裝置範本]  對話方塊中，選擇[發佈]  ：
  
    ![檢視 - 發佈](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

裝置範本發佈後，將會顯示在 [裝置]  頁面上，並且對操作員顯示。 在已發佈的裝置範本中，您無法直接編輯裝置功能模型而不建立新版本。 不過，您可以直接在已發佈的裝置範本中更新雲端屬性、自訂和檢視，而無須進行版本控制。 進行任何變更之後，請選取 [發佈]  ，將這些變更推送給您的操作員。
  
![檢視 - 發佈](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

* 建立新的 Edge 作為分葉裝置範本
* 從上傳的部署資訊清單產生模組
* 新增複雜類型的遙測和屬性
* 建立雲端屬性。
* 建立自訂。
* 定義裝置遙測的視覺效果。
* 發佈您的 Edge 裝置範本。

現在，您已在 Azure IoT Central 應用程式中建立裝置範本，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [連接裝置](./tutorial-connect-pnp-device.md)
