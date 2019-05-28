---
title: 教學課程：設定 Azure 時間序列深入解析預覽版環境 | Microsoft Docs
description: 了解如何在 Azure 時間序列深入解析預覽版中設定環境。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713907"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>教學課程：設定 Azure 時間序列深入解析預覽版環境

本教學課程會逐步引導您完成建立「Azure 時間序列深入解析預覽版」預付型方案 (PAYG) 環境的程序。 在本教學課程中，您了解如何：

* 建立 Azure 時間序列深入解析預覽版環境。
* 將 Azure 時間序列深入解析預覽版環境連線至 Azure 事件中樞內的某個事件中樞。
* 執行解決方案加速器範例，以將資料串流處理至「Azure 時間序列深入解析預覽版」環境。
* 在資料上執行基本的分析。
* 定義「時間序列模型」類型和階層，並將其與您的執行個體建立關聯。

# <a name="create-a-device-simulation"></a>建立裝置模擬

在本節中，您會建立三個將資料傳送至 IoT 中樞的模擬裝置。

1. 移至 [Azure IoT 解決方案加速器頁面](https://www.azureiotsolutions.com/Accelerators)。 此頁面會顯示數個預先建置的範例。 請使用您的 Azure 帳戶來登入。 然後，選取 [裝置模擬]  。

   [![Azure IoT 解決方案加速器](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox) 頁面

   選取 [立即試用]  。

1. 在 [建立裝置模擬解決方案]  頁面上輸入必要的參數：

   | 參數 | 說明 |
   | --- | --- |
   | **解決方案名稱** |    輸入一個唯一值來建立新的資源群組。 列出的 Azure 資源會在建立後指派給資源群組。 |
   | **訂用帳戶** | 指定與用於建立「時間序列深入解析」環境相同的訂用帳戶。 |
   | **區域** |   指定與用於建立「時間序列深入解析」環境相同的區域。 |
   | **部署選擇性 Azure 資源**    | 將 [IoT 中樞] 保留為已勾選，因為模擬裝置將會使用它來連接及串流處理資料。 |

   然後，選取 [建立解決方案]  。 等候 10 到 15 分鐘來部署解決方案。

   [![建立裝置模擬解決方案](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox) 頁面

1. 在解決方案加速器儀表板中，選取 [啟動]  按鈕：

   [![啟動裝置模擬解決方案](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. 系統會將您重新導向至 [Microsoft Azure IoT 裝置模擬]  頁面。 選取頁面右上方的 [+ New simulation] \(+ 新增模擬\)  。

   [![Azure IoT 模擬頁面](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. 如下所示填寫必要的參數：

    [![要填寫的參數](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **名稱** | 為模擬器輸入唯一的名稱。 |
    | **說明** | 輸入定義。 |
    | **模擬持續時間** | 設定為 [Run indefinitely] \(無限期執行\)  。 |
    | **裝置型號** | **名稱**：輸入 `Chiller` 。 </br>**數量**：輸入 `3` 。 |
    | **目標 IoT 中樞** | 設定為 [Use pre-provisioned IoT Hub] \(使用預先佈建的 IoT 中樞\)  。 |

    然後，選取 [開始模擬]  。

1. 在裝置模擬儀表板中，查看 [	使用中的裝置]  和 [Messages per second] \(每秒訊息數\)  。

    [![Azure IoT 模擬儀表板](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>列出裝置模擬屬性

在建立「Azure 時間序列深入解析」環境之前，您需要有 IoT 中樞、訂用帳戶及資源群組的名稱。

1. 移至解決方案加速器儀表板，然後使用相同 Azure 訂用帳戶的帳戶來登入。 尋找您在先前的步驟中建立的裝置模擬。

1. 選取您的裝置模擬器，然後選取 [啟動]  。 選取右邊的 [Azure 管理入口網站]  連結。

    [![模擬器清單](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. 記下 IoT 中樞、訂用帳戶及資源群組的名稱。

    [![Azure 入口網站](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>建立時間序列深入解析預覽版 PAYG 環境

本節說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立 Azure 時間序列深入解析預覽版環境。

1. 使用訂用帳戶登入 Azure 入口網站。

1. 選取 [建立資源]  。

1. 選取 [物聯網]  類別，然後選取 [時間序列深入解析]  。

   [![選取 [物聯網]，然後選取 [時間序列深入解析]](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. 依下列方式填寫頁面上的欄位：

   | | |
   | --- | ---|
   | **環境名稱** | 為 Azure 時間序列深入解析預覽版環境選擇唯一的名稱。 |
   | **訂用帳戶** | 輸入您要在其中建立 Azure 時間序列深入解析預覽版環境的訂用帳戶。 您使用的訂用帳戶最好與裝置模擬器所建立的其餘 IoT 資源相同。 |
   | **資源群組** | 資源群組是 Azure 資源的容器。 請為 Azure 時間序列深入解析預覽版環境資源選擇現有的資源群組，或建立新的群組。 您使用的資源群組最好與裝置模擬器所建立的其餘 IoT 資源相同。 |
   | **位置** | 為「Azure 時間序列深入解析預覽版」環境選擇資料中心區域。 為了避免增加頻寬成本和延遲時間，建議您將 Azure 時間序列深入解析預覽版環境設置於與其他 IoT 資源相同的區域中。 |
   | **層級** |  選取代表預付型方案的 [PAYG]  。 這是適用於「Azure 時間序列深入解析預覽版」產品的 SKU。 |
   | **屬性識別碼** | 輸入可唯一識別時間序列的識別碼。 請注意，此欄位是不可變的欄位，之後也無法變更。 針對本教學課程，請使用 `iothub-connection-device-id`。 若要深入了解「時間序列識別碼」，請參閱[如何選擇時間序列識別碼](./time-series-insights-update-how-to-id.md)。 |
   | **儲存體帳戶名稱** | 針對要建立的新儲存體帳戶輸入全域唯一的名稱。 |

   然後，選取 [下一步:  事件來源]。

   [![建立「時間序列深入解析」環境的頁面](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. 在事件來源的頁面上，依下列方式填寫欄位：

   | | |
   | --- | --- |
   | **建立事件來源?** | 輸入 `Yes` 。|
   | **名稱** | 輸入用來為事件來源命名的唯一值。|
   | **來源類型** | 選取 [IoT 中樞]  。 |
   | **選取中樞?** | 選取 [選取現有的]  。 |
   | **訂用帳戶** | 選取用於裝置模擬器的訂用帳戶。 |
   | **IoT 中樞名稱** | 選取您為裝置模擬器建立的 IoT 中樞名稱。 |
   | **IoT 中樞存取原則** | 選取 [iothubowner]  。 |
   | **IoT 中樞取用者群組** | 「Azure 時間序列深入解析預覽版」需要一個唯一的取用者群組。 選取 [新增]  ，輸入唯一名稱，然後再選取 [新增]  。 |
   | **時間戳記屬性** | 此欄位用來識別傳入遙測資料中的時間戳記屬性。 針對本教學課程，請勿填寫此欄位。 此模擬器會使用「時間序列深入解析」的預設「IoT 中樞」所傳來的時間戳記。|

   然後，選取 [檢閱 + 建立]  。

   [![設定事件來源](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. 檢閱 [檢閱] 頁面上的所有欄位，然後選取 [建立]  。

   [![[檢閱 + 建立] 頁面，其中含有 [建立] 按鈕](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. 您可以看到部署的狀態。

   [![部署已完成的通知](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. 如果您擁有該租用戶，應該就會獲得「Azure 時間序列深入解析預覽版」環境的存取權。 若要確定您擁有存取權：

   a. 搜尋資源群組並選取您的「Azure 時間序列深入解析預覽版」環境：

      [![選取的環境](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. 在「Azure 時間序列深入解析預覽版」頁面上，移至 [資料存取原則]  。

     [![資料存取原則](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. 確認您的認證是否有列出。

     [![列出的認證](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   如果未列出您的認證，您就必須為自己提供環境的存取權限。 若要深入了解如何設定權限，請參閱[授與資料存取權](./time-series-insights-data-access.md)。

## <a name="analyze-data-in-your-environment"></a>在您的環境中分析資料

在本節中，您會使用 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)，對時間序列資料執行基本分析。

1. 從 [Azure 入口網站](https://portal.azure.com/)的資源頁面選取 URL，以移至「Azure 時間序列深入解析預覽版」總管。

   [![時間序列深入解析預覽版總管 URL](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. 在總管中，選取 [Time Series Instances] \(時間序列執行個體\)  節點，以查看環境中的所有「Azure 時間序列深入解析預覽版」執行個體。

   [![無上層執行個體的清單](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. 在所顯示的時間序列中，選取第一個執行個體。 然後，選取 [Show+Avg+pressure] \(顯示平均壓力\)  。

   [![已選取的執行個體，其中含有顯示平均壓力的功能表命令](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   時間序列圖表應該會出現在右邊。 將 [間隔]  調整為 `15s`。

   [![時間序列圖表](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. 對另外兩個時間序列重複執行**步驟 3**。 接著，您便可以檢視所有時間序列，如以下圖表所示：

   [![所有時間序列的圖表](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. 修改時間範圍以查看過去一小時的時間序列趨勢。

   a. 選取 [時間範圍]  選項方塊：

      [![將時間範圍設定為 1 小時](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>定義並套用模型

在本節中，您會套用模型來建構資料。 為了完成此模型，您會定義類型、階層和執行個體。 若要深入了解資料模型化，請移至[時間序列模型](./time-series-insights-update-tsm.md)。

1. 在總管中，選取 [模型]  索引標籤：

   [![總管中的 [模型] 索引標籤](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. 選取 [+ 新增]  來新增類型。 右側會開啟類型編輯器。

   [![類型的 [新增] 按鈕](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. 為類型定義三個變數：壓力、溫度及濕度。 輸入以下資訊：

   | | |
   | --- | ---|
   | **名稱** | 輸入 `Chiller` 。 |
   | **說明** | 輸入 `This is a type definition of Chiller` 。 |

   * 使用三個變數來定義壓力：

      | | |
      | --- | ---|
      | **名稱** | 輸入 `Avg Pressure` 。 |
      | **值** | 選取 [壓力 (雙精準數)]  。 請注意，在「Azure 時間序列深入解析預覽版」開始接收事件之後，可能需要幾分鐘的時間才會填入此欄位。 |
      | **彙總作業** | 選取 [AVG]  。 |

      [![用於定義壓力的選取項目](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      選取 [+ 加入變數]  來新增下一個變數。

   * 定義溫度：

      | | |
      | --- | ---|
      | **名稱** | 輸入 `Avg Temperature` 。 |
      | **值** | 選取 [溫度 (雙精準數)]  。 請注意，在「Azure 時間序列深入解析預覽版」開始接收事件之後，可能需要幾分鐘的時間才會填入此欄位。 |
      | **彙總作業** | 選取 [AVG]  。|

      [![用於定義溫度的選取項目](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * 定義濕度：

      | | |
      | --- | ---|
      | **名稱** | 輸入 `Max Humidity` |
      | **值** | 選取 [溼度 (雙精準數)]  。 請注意，在「Azure 時間序列深入解析預覽版」開始接收事件之後，可能需要幾分鐘的時間才會填入此欄位。 |
      | **彙總作業** | 選取 [MAX]  。|

      [![用於定義溫度的選取項目](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   然後，選取 [Create]  \(建立\)。

1. 您會看到類型已新增：

   [![已新增之類型的相關資訊](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. 下一個步驟是新增階層。 在 [階層]  區段中，選取 [+ 新增]  ：

   [![[階層] 索引標籤，其中含有 [新增] 按鈕](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. 定義階層。 依下列方式填寫欄位：

   | | |
   | --- | ---|
   | **名稱** | 輸入 `Location Hierarchy` 。 |
   | **層級 1** | 輸入 `Country` 。 |
   | **層級 2** | 輸入 `City` 。 |
   | **層級 3** | 輸入 `Building` 。 |

   然後，選取 [Create]  \(建立\)。

   [![階層欄位，其中含有 [建立] 按鈕](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. 您會看到您建立的階層：

   [![階層的相關資訊](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. 選取左側的 [執行個體]  。 在執行個體出現之後，選取第一個執行個體，然後選取 [編輯]  ：

   [![選取執行個體的 [編輯] 按鈕](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. 右側會出現文字編輯器。 加入下列資訊：

   | | |
   | --- | --- |
   | **類型** | 選取 [Chiller]  。 |
   | **說明** | 輸入 `Instance for Chiller-01.1` 。 |
   | **階層** | 選取 [位置階層]  。 |
   | **國家/地區** | 輸入 `USA` 。 |
   | **城市** | 輸入 `Seattle` 。 |
   | **建築物** | 輸入 `Space Needle` 。 |

    然後選取 [儲存]  。

   [![執行個體欄位，其中含有 [儲存] 按鈕](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. 針對其他感應器重複上一個步驟。 使用下列欄位：

   * 針對 Chiller 01.2：

     | | |
     | --- | --- |
     | **類型** | 選取 [Chiller]  。 |
     | **說明** | 輸入 `Instance for Chiller-01.2` 。 |
     | **階層** | 選取 [位置階層]  。 |
     | **國家/地區** | 輸入 `USA` 。 |
     | **城市** | 輸入 `Seattle` 。 |
     | **建築物** | 輸入 `Pacific Science Center` 。 |

   * 針對 Chiller 01.3：

     | | |
     | --- | --- |
     | **類型** | 選取 [Chiller]  。 |
     | **說明** | 輸入 `Instance for Chiller-01.3` 。 |
     | **階層** | 選取 [位置階層]  。 |
     | **國家/地區** | 輸入 `USA` 。 |
     | **城市** | 輸入 `New York` 。 |
     | **建築物** | 輸入 `Empire State Building` 。 |

1. 移至 [分析]  索引標籤，然後重新整理頁面。 展開所有階層層級以尋找時間序列。

   [![[分析] 索引標籤](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. 若要瀏覽過去一小時的時間序列，請將 [快速時間]  變更為 [最近 1 小時]  ：

    [![[快速時間] 方塊，其中已選取 [最近 1 小時]](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. 選取 [太平洋科學中心]  底下的時間序列，然後選取 [Show Max Humidity] \(顯示最大溼度\)  。

    [![已選取的時間序列，其中含有 [Show Max Humidity] \(顯示最大溼度\) 功能表選取項目](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. 隨即會開啟間隔大小為 [1 分鐘]  的 [Max Humidity] \(最大溼度\)  時間序列。 請選取區域來篩選範圍。 然後，按一下滑鼠右鍵並遠取 [縮放]  ，以分析時間範圍內的事件：

   [![已選取的範圍，其中捷徑功能表上含有 [縮放] 命令](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. 您也可以選取某個區域，然後按一下滑鼠右鍵來查看事件詳細資料：

   [![詳細的事件清單](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：  

> [!div class="checklist"]
> * 建立並使用裝置模擬加速器。
> * 建立 Azure 時間序列深入解析預覽版 PAYG 環境。
> * 將 Azure 時間序列深入解析預覽版環境連線至某個事件中樞。
> * 執行解決方案加速器範例，以將資料串流處理至「Azure 時間序列深入解析預覽版」環境。
> * 執行資料的基本分析。
> * 定義「時間序列模型」類型和階層，並將其與您的執行個體建立關聯。

現在您已經知道如何建立自己的 Azure 時間序列深入解析預覽版環境，接下來可以進一步了解Azure 時間序列深入解析中的重要概念。

了解「Azure 時間序列深入解析」儲存體設定：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析預覽版儲存體和輸入](./time-series-insights-update-storage-ingress.md)

深入了解時間序列模型：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析預覽版資料模型化](./time-series-insights-update-tsm.md)