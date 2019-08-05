---
title: 教學課程：設定 Azure 時間序列深入解析預覽版環境 | Microsoft Docs
description: 了解如何在 Azure 時間序列深入解析預覽版中設定環境。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.custom: seodec18
ms.openlocfilehash: b462f0c427020b1422b91fbd7ac34e3023e546b8
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677874"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>教學課程：設定 Azure 時間序列深入解析預覽版環境

本教學課程會逐步引導您完成建立「Azure 時間序列深入解析預覽版」隨用隨付 (PAYG) 環境的程序。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure 時間序列深入解析預覽版環境。
> * 將 Azure 時間序列深入解析預覽版環境連線至 Azure 事件中樞內的某個事件中樞。
> * 執行解決方案加速器範例，以將資料串流處理至「Azure 時間序列深入解析預覽版」環境。
> * 在資料上執行基本的分析。
> * 定義「時間序列模型」類型和階層，並將其與您的執行個體建立關聯。

>[!TIP]
> [IoT 解決方案加速器](https://www.azureiotsolutions.com/Accelerators)提供預先設定的企業級解決方案，可讓您用來加快自訂 IoT 解決方案的開發速度。

如果您沒有帳戶，請註冊[免費 Azure 訂用帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* 您的 Azure 登入帳戶也必須是訂用帳戶的**擁有者**角色成員。 如需詳細資訊，請參閱[使用角色型存取控制和 Azure 入口網站管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="create-a-device-simulation"></a>建立裝置模擬

在本節中，您會建立三個將資料傳送至 Azure IoT 中樞執行個體的模擬裝置。

1. 移至 [Azure IoT 解決方案加速器頁面](https://www.azureiotsolutions.com/Accelerators)。 此頁面會顯示數個預先建置的範例。 請使用您的 Azure 帳戶來登入。 然後，選取 [裝置模擬]  。

   [![Azure IoT 解決方案加速器](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox) 頁面

   選取 [立即試用]  。

1. 在 [建立裝置模擬解決方案]  頁面上，設定下列參數：

    | 參數 | 動作 |
    | --- | --- |
    | **部署名稱** | 為新的資源群組輸入唯一值。 列出的 Azure 資源會在建立後指派給資源群組。 |
    | **Azure 訂用帳戶** | 選取用來建立「時間序列深入解析」環境的訂用帳戶。 |
    | **Azure 位置** | 選取用來建立「時間序列深入解析」環境的區域。 |
    | **部署選項** | 選取 [佈建新的 IoT 中樞]  。 |
 
    選取 [建立解決方案]  。 需要 20 分鐘的時間讓解決方案完成部署。

    [![建立裝置模擬解決方案](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox) 頁面

## <a name="create-a-time-series-insights-preview-payg-environment"></a>建立時間序列深入解析預覽版 PAYG 環境

本節說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立 Azure 時間序列深入解析預覽環境，並且將其連接至 IoT 解決方案加速器建立的 IoT 中樞。

1. 使用訂用帳戶登入 Azure 入口網站。

1. 選取 [建立資源]   > [物聯網]   > [時間序列深入解析]  。

   [![選取 [物聯網]，然後選取 [時間序列深入解析]](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. 在 [建立時間序列深入解析環境]  窗格的 [基本資料]  索引標籤上，設定下列參數：

    | 參數 | 動作 |
    | --- | ---|
    | **環境名稱** | 為 Azure 時間序列深入解析預覽版環境輸入唯一的名稱。 |
    | **訂用帳戶** | 輸入您要在其中建立 Azure 時間序列深入解析預覽版環境的訂用帳戶。 您使用的訂用帳戶最好和裝置模擬器所建立的其餘 IoT 資源相同。 |
    | **資源群組** | 請為 Azure 時間序列深入解析預覽版環境資源選取現有的資源群組，或建立新的資源群組。 資源群組是 Azure 資源的容器。 您使用的資源群組最好與裝置模擬器所建立的其餘 IoT 資源相同。 |
    | **位置** | 為「Azure 時間序列深入解析預覽版」環境選取資料中心區域。 為了避免增加額外延遲時間，建議您將 Azure 時間序列深入解析預覽版環境建立於與其他 IoT 資源相同的區域中。 |
    | **層級** |  選取 [PAYG]  ([隨用隨付]  )。 這是適用於「Azure 時間序列深入解析預覽版」產品的 SKU。 |
    | **屬性識別碼** | 輸入可唯一識別時間序列執行個體的值。 您在 [屬性識別碼]  方塊中輸入的值是不可變的。 之後將無法加以變更。 針對本教學課程，請輸入 **iothub-connection-device-id**。若要深入了解時間序列識別碼，請參閱[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)。 |
    | **儲存體帳戶名稱** | 針對要建立的新儲存體帳戶輸入全域唯一的名稱。 |
   
   完成時，選取 [下一步:  事件來源]。

   [![建立「時間序列深入解析」環境的窗格](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. 在 [事件來源]  索引標籤上，設定下列參數：

   | 參數 | 動作 |
   | --- | --- |
   | **建立事件來源?** | 選取 [是]  。|
   | **名稱** | 為事件來源名稱輸入唯一值。 |
   | **來源類型** | 選取 [IoT 中樞]  。 |
   | **選取中樞** | 選擇 [選取現有的]  。 |
   | **訂用帳戶** | 選取用於裝置模擬器的訂用帳戶。 |
   | **IoT 中樞名稱** | 選取您為裝置模擬器建立的 IoT 中樞名稱。 |
   | **IoT 中樞存取原則** | 選取 [iothubowner]  。 |
   | **IoT 中樞取用者群組** | 選取 [新增]  ，輸入唯一名稱，然後再選取 [新增]  。 在 Azure 時間序列深入解析預覽版中，取用者群組必須是唯一值。 |
   | **時間戳記屬性** | 此值可用來識別傳入遙測資料中的**時間戳記**屬性。 在此教學課程中，將此方塊保留空白。 此模擬器會使用「時間序列深入解析」的預設「IoT 中樞」所傳來的時間戳記。 |

   選取 [檢閱 + 建立]  。

   [![設定事件來源](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. 在 [檢閱 + 建立]  索引標籤上，檢閱您的選取項目，然後選取 [建立]  。

    [![[檢閱 + 建立] 頁面，其中含有 [建立] 按鈕](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    您可以看到部署的狀態：

    [![部署已完成的通知](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. 如果您擁有該租用戶，就能存取「Azure 時間序列深入解析預覽版」環境。 若要確定您擁有存取權：

   1. 搜尋資源群組，然後選取您的「Azure 時間序列深入解析預覽版」環境：

      [![選取的環境](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. 在「Azure 時間序列深入解析預覽版」頁面上，選取 [資料存取原則]  ：

      [![資料存取原則](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. 確認您的認證是否有列出：

      [![列出的認證](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   如果未列出您的認證，您就必須對自己授與環境的存取權限。 若要深入了解如何設定權限，請參閱[授與資料存取權](./time-series-insights-data-access.md)。

## <a name="stream-data-into-your-environment"></a>將資料串流到您的環境

1. 瀏覽回到 [Azure IoT 解決方案加速器頁面](https://www.azureiotsolutions.com/Accelerators)。 在解決方案加速器儀表板中，尋找您的解決方案。 然後選取 [啟動]  ：

    [![啟動裝置模擬解決方案](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. 系統會將您重新導向至 [Microsoft Azure IoT 裝置模擬]  頁面。 在頁面的右上角，選取 [新增模擬]  。

    [![Azure IoT 模擬頁面](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. 在 [模擬設定]  窗格中，設定下列參數：

    | 參數 | 動作 |
    | --- | --- |
    | **名稱** | 為模擬器輸入唯一的名稱。 |
    | **說明** | 輸入定義。 |
    | **模擬持續時間** | 設定為 [Run indefinitely] \(無限期執行\)  。 |
    | **裝置型號** | **名稱**：輸入 **Chiller**。 <br />**數量**：輸入 **3**。 |
    | **目標 IoT 中樞** | 設定為 [Use pre-provisioned IoT Hub] \(使用預先佈建的 IoT 中樞\)  。 |

    [![要設定的參數](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    選取 [開始模擬]  。

    在裝置模擬儀表板中，記下 [作用中裝置]  和 [每秒訊息數]  所顯示的資訊。

    [![Azure IoT 模擬儀表板](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data-in-your-environment"></a>在您的環境中分析資料

在本節中，您會使用 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)，對時間序列資料執行基本分析。

1. 從 [Azure 入口網站](https://portal.azure.com/)的資源頁面選取 URL，以移至「Azure 時間序列深入解析預覽版」總管。

    [![時間序列深入解析預覽版總管 URL](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. 在總管中，選取 [Time Series Instances] \(時間序列執行個體\)  節點，以查看環境中的所有「Azure 時間序列深入解析預覽版」執行個體。

    [![無上層執行個體的清單](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. 選取第一個時間序列執行個體。 然後，選取 [顯示壓力]  。

    [![已選取的時間序列執行個體，其中含有顯示平均壓力的功能表命令](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    時間序列圖隨即出現。 將 [間隔]  變更為 [15 秒]  。

    [![時間序列圖表](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. 對另外兩個時間序列執行個體重複執行步驟 3。 您可以檢視所有時間序列執行個體，如以下圖表所示：

    [![所有時間序列的圖表](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. 在 [時間範圍]  選項方塊中，修改時間範圍以查看過去一小時的時間序列趨勢：

    [![將時間範圍設定為 1 小時](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>定義並套用模型

在本節中，您會套用模型來建構資料。 為了完成此模型，您會定義類型、階層和執行個體。 若要深入了解資料模型化，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

1. 在總管中，選取 [模型]  索引標籤：

   [![總管中的 [模型] 索引標籤](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. 選取 [新增]  來新增類型：

   [![類型的 [新增] 按鈕](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. 接下來，為類型定義三個變數：壓力  、溫度  及濕度  。 在 [新增類型]  窗格中，設定下列參數：

    | 參數 | 動作 |
    | --- | ---|
    | **名稱** | 輸入 **Chiller**。 |
    | **說明** | 輸入**這是 Chiller 的類型定義**。 |

   * 若要定義 [壓力]  ，請於 [變數]  底下設定下列參數：

     | 參數 | 動作 |
     | --- | ---|
     | **名稱** | 輸入**平均壓力**。 |
     | **值** | 選取 [壓力 (雙精準數)]  。 在「Azure 時間序列深入解析預覽版」開始接收事件之後，可能需要幾分鐘的時間才會自動填入 [值]  。 |
     | **彙總作業** | 選取 [AVG]  。 |

      [![用於定義壓力的選取項目](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      若要新增下一個變數，請選取 [新增變數]  。

   * 定義溫度  ：

     | 參數 | 動作 |
     | --- | ---|
     | **名稱** | 輸入**平均溫度**。 |
     | **值** | 選取 [溫度 (雙精準數)]  。 在「Azure 時間序列深入解析預覽版」開始接收事件之後，可能需要幾分鐘的時間才會自動填入 [值]  。 |
     | **彙總作業** | 選取 [AVG]  。|

      [![用於定義溫度的選取項目](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      若要新增下一個變數，請選取 [新增變數]  。

   * 定義濕度  ：

      | | |
      | --- | ---|
      | **名稱** | 輸入**最大溼度** |
      | **值** | 選取 [溼度 (雙精準數)]  。 在「Azure 時間序列深入解析預覽版」開始接收事件之後，可能需要幾分鐘的時間才會自動填入 [值]  。 |
      | **彙總作業** | 選取 [MAX]  。|

      [![用於定義溫度的選取項目](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    選取 [建立]  。

    您會看到您新增的類型：

    [![已新增之類型的相關資訊](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. 下一個步驟是新增階層。 在 [階層]  底下，選取 [新增]  ：

    [![[階層] 索引標籤，其中含有 [新增] 按鈕](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. 在 [編輯階層]  窗格中，設定下列參數：

   | 參數 | 動作 |
   | --- | ---|
   | **名稱** | 輸入**位置階層**。 |
   | **層級 1** | 輸入**國家/地區**。 |
   | **層級 2** | 輸入**縣市**。 |
   | **層級 3** | 輸入**建築物**。 |

   選取 [ **儲存**]。

    [![階層欄位，其中含有 [建立] 按鈕](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   您會看到您建立的階層：

    [![階層的相關資訊](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. 選取 [執行個體]  。 選取第一個執行個體，然後選取 [編輯]  ：

    [![選取執行個體的 [編輯] 按鈕](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. 在 [編輯執行個體]  窗格中，設定下列參數：

    | 參數 | 動作 |
    | --- | --- |
    | **類型** | 選取 [Chiller]  。 |
    | **說明** | 輸入 **Chiller-01.1 的執行個體**。 |
    | **階層** | 選取 [位置階層]  。 |
    | **國家/地區** | 輸入**美國**。 |
    | **城市** | 輸入**西雅圖**。 |
    | **建築物** | 輸入**太空針塔**。 |

    [![執行個體欄位，其中含有 [儲存] 按鈕](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   選取 [ **儲存**]。

1. 針對其他感應器重複上一個步驟。 更新下列值：

   * 針對 Chiller 01.2：

     | 參數 | 動作 |
     | --- | --- |
     | **類型** | 選取 [Chiller]  。 |
     | **說明** | 輸入 **Chiller-01.2 的執行個體**。 |
     | **階層** | 選取 [位置階層]  。 |
     | **國家/地區** | 輸入**美國**。 |
     | **城市** | 輸入**西雅圖**。 |
     | **建築物** | 輸入**太平洋科學中心**。 |

   * 針對 Chiller 01.3：

     | 參數 | 動作 |
     | --- | --- |
     | **類型** | 選取 [Chiller]  。 |
     | **說明** | 輸入 **Chiller-01.3 的執行個體**。 |
     | **階層** | 選取 [位置階層]  。 |
     | **國家/地區** | 輸入**美國**。 |
     | **城市** | 輸入**紐約**。 |
     | **建築物** | 輸入**帝國大廈**。 |

1. 選取 [分析]  索引標籤，然後重新整理頁面。 在 [位置階層]  底下，展開所有階層層級來顯示時間序列執行個體：

   [![[分析] 索引標籤](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. 若要瀏覽過去一小時的時間序列執行個體，請將 [快速時間]  變更為 [最近 1 小時]  ：

    [![[快速時間] 方塊，其中已選取 [最近 1 小時]](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. 在 [太平洋科學中心]  底下，依序選取時間序列執行個體和 [顯示最大溼度]  。

    [![已選取的時間序列執行個體，以及 [顯示最大溼度] 功能表選取項目](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. 隨即會開啟間隔大小為 [1 分鐘]  的 [Max Humidity] \(最大溼度\)  時間序列。 若要篩選範圍，請選取某個區域。 若要分析時間範圍內的事件，請對圖表按一下滑鼠右鍵，然後選取 [縮放]  ：

   [![已選取的範圍，其中捷徑功能表上含有 [縮放] 命令](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. 若要查看事件詳細資料，請選取某個區域，然後對圖表按一下滑鼠右鍵：

   [![詳細的事件清單](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>清除資源

您現在已完成本教學課程，請清除您所建立的資源：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [所有資源]  ，找出 Azure 時間序列深入解析資源群組。
1. 選取 [刪除]  或個別移除每個資源，以刪除整個資源群組 (和其中包含的所有資源)。

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
