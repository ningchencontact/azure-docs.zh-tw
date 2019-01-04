---
title: Azure 時間序列深入解析預覽版設定 - 設定 Azure 時間序列深入解析預覽版環境教學課程 | Microsoft Docs
description: 了解如何在 Azure 時間序列深入解析預覽版中設定環境。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322613"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>教學課程：設定 Azure 時間序列深入解析預覽版環境

本教學課程會逐步引導您完成建立 Azure 時間序列深入解析預付型方案 (PAYG) 預覽版環境的程序。 在本教學課程中，您了解如何：

* 建立 Azure 時間序列深入解析預覽版環境。
* 將 Azure 時間序列深入解析預覽版環境連線至 Azure 事件中樞內的某個事件中樞。
* 執行風力發電廠模擬以將資料串流處理至 Azure 時間序列深入解析預覽版環境中。
* 在資料上執行基本的分析。
* 定義時間序列模型類型和階層，並將其與您的執行個體產生關聯。

# <a name="create-a-device-simulation"></a>建立裝置模擬

在本節中，您會建立三個將資料傳送至 IoT 中樞的模擬裝置。

1. 移至 [Azure IoT 解決方案加速器首頁](https://www.azureiotsolutions.com/Accelerators)。 Azure IoT 解決方案加速器首頁上會顯示數個預先建置的範例。 使用 Azure 帳戶來登入。 然後，選取 [裝置模擬]。

   ![Azure IoT 解決方案加速器首頁][1]

   最後，按一下 [立即試用]。

1. 在 [建立裝置模擬] 解決方案頁面上輸入必要的參數：

   | 參數 | 說明 |
   | --- | --- |
   | 方案名稱 |    一個唯一值，用來建立新的資源群組。 列出的 Azure 資源會在建立後指派給資源群組。 | created and assigned to the resource group. |
   | 訂用帳戶 | 指定用於建立 TSI 環境的相同訂用帳戶 |
   | 區域 |   指定用於建立 TSI 的相同區域。 |
   | 部署選擇性 Azure 資源    | 將 [IoT 中樞] 保留為已勾選，因為模擬裝置將會用它來連接/串流處理資料。 |

   在輸入必要的參數之後，請按一下 [建立解決方案]。 等候大約 10 至 15 分鐘來部署解決方案。

   ![建立裝置模擬解決方案][2]

1. 在 [解決方案加速器儀表板] 中，按一下 [啟動] 按鈕：

   ![啟動裝置模擬解決方案][3]

1. 系統會將您重新導向至 [Microsoft Azure IoT 裝置模擬] 頁面。 按一下位於畫面右上方的 [+ 新增模擬]。

   ![Azure IoT 模擬頁面][4]

1.  如下所示填寫必要的參數：

    ![要填寫的參數][5]

    |||
    | --- | --- |
    | **名稱** | 為模擬器輸入唯一的名稱 |
    | **說明** | 輸入定義 |
    | **模擬持續時間** | 設定為 `Run indefinitely` |
    | **裝置型號** | **名稱**：輸入 `Chiller` **數量**：輸入 `3` |
    | **目標 IoT 中樞** | 設定為 `Use pre-provisioned IoT Hub` |

    在填入必要的參數之後，請按一下 [開始模擬]。

1. 在裝置模擬儀表板中，請查看 [作用中裝置] 和 [每秒訊息數]。

    ![Azure IoT 模擬儀表板][6]

## <a name="list-device-simulation-properties"></a>列出裝置模擬屬性

在建立 Azure 時間序列深入解析環境之前，您需要有 IoT 中樞的名稱、訂用帳戶和資源群組名稱。

1. 移至 [解決方案加速器儀表板]，然後使用相同的 Azure 訂用帳戶來登入。 尋找您在先前的步驟中建立的裝置模擬。

1. 按一下裝置模擬器，然後按一下 [啟動]。 按一下右手邊顯示的 [Azure 管理入口網站] 連結。

    ![模擬器清單][7]

1. 記下 IoT 中樞、訂用帳戶和資源群組的名稱。

    ![Azure 入口網站][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>建立時間序列深入解析預覽版 PAYG 環境

本節說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立 Azure 時間序列深入解析預覽版環境。

1. 使用訂用帳戶登入 Azure 入口網站。

1. 選取 [建立資源]。

1. 選取 [物聯網] 類別，然後選取 [時間序列深入解析]。

   ![依序選取 [建立資源]、[物聯網] 和 [時間序列深入解析]][9]

1. 如下所示填寫頁面上的欄位：

   | | |
   | --- | ---|
   | **環境名稱** | 為 Azure 時間序列深入解析預覽版環境選擇唯一的名稱。 |
   | **訂用帳戶** | 輸入您要在其中建立 Azure 時間序列深入解析預覽版環境的訂用帳戶。 您使用的訂用帳戶最好和裝置模擬器所建立的其餘 IoT 資源相同。 |
   | **資源群組** | 資源群組是 Azure 資源的容器。 請為 Azure 時間序列深入解析預覽版環境資源選擇現有的資源群組，或建立新的群組。 您使用的資源群組最好和裝置模擬器所建立的其餘 IoT 資源相同。 |
   | **位置** | 為 Azure 時間序列深入解析預覽版環境選擇資料中心區域。 為了避免增加頻寬成本和延遲時間，建議您將 Azure 時間序列深入解析預覽版環境設置於與其他 IoT 資源相同的區域中。 |
   | **層級** |  選取 `PAYG`，其代表預付型方案。 這是適用於 Azure 時間序列深入解析預覽版產品的 SKU。 |
   | **屬性識別碼** | 可唯一識別時間序列。 請注意，此為不可變欄位，之後就無法變更。 在本教學課程中，請將此欄位設定為 `iothub-connection-device-id`。 若要深入了解時間序列識別碼，請閱讀[如何選擇時間序列識別碼](./time-series-insights-update-how-to-id.md)。 |
   | **儲存體帳戶名稱** | 針對要建立的新儲存體帳戶輸入全域唯一的名稱。 |

   在填寫完上述欄位之後，請按 [下一步: 事件來源]。

   ![按 [下一步: 事件來源]][10]

1. 在頁面上，如下所示填妥欄位：

   | | |
   | --- | --- |
   | **建立事件來源？** | 輸入 `Yes`|
   | **名稱** | 需要唯一值，用來為事件來源命名。|
   | **來源類型** | 輸入 `IoT Hub` |
   | **選取中樞？** | 輸入 `Select Existing` |
   | **訂用帳戶** | 輸入裝置模擬器所使用的訂用帳戶。 |
   | **IoT 中樞名稱** | 輸入您為裝置模擬器建立的 IoT 中樞名稱。 |
   | **IoT 中樞存取原則** | 輸入 `iothubowner` |
   | **IoT 中樞取用者群組** | Azure 時間序列深入解析預覽版需要唯一的取用者群組。 |
   | **Timestamp** | 此欄位用來識別傳入遙測資料中的時間戳記屬性。 在本教學課程中，請勿填寫此欄位。 此模擬器會使用時間序列深入解析的預設 IoT 中樞所傳來的時間戳記。|

   若要建立唯一的取用者群組：

   1. 按一下 [IoT 中樞取用者群組] 欄位旁的 [新增]：

      ![按 下一步: 事件來源][11]

   1. 為取用者群組提供唯一的名稱，然後按一下 [新增]：

      ![按一下 [新增]][12]

   填寫完上述欄位後，按一下 [檢閱 + 建立]。

      ![檢閱並建立][13]

1. 檢閱 [檢閱] 頁面中的所有欄位，然後按一下 [建立]。

   ![建立][14]

1. 您可以看到部署的狀態。

   ![部署完成][15]

1. 如果您擁有該租用戶，應該就會獲得時間序列環境的存取權。 若要確定您擁有存取權：

   * 瀏覽至您新建立的 Azure 時間序列深入解析預覽版環境。 若要這麼做，請搜尋資源群組。 然後，對您的時間序列環境按一下：

      ![部署完成][16]

   * 在 Azure 時間序列深入解析預覽版的頁面上，瀏覽至 [資料存取原則]。

     ![資料存取原則][17]

   * 確認您的認證是否有列出。

     ![確認認證][18]

   如果您的認證未列出，您就必須為自己提供環境的存取權限。 請閱讀[授與資料存取](./time-series-insights-data-access.md)以深入了解設定權限。

## <a name="analyze-data-in-your-environment"></a>在您的環境中分析資料

在本節中，您會使用 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)，對時間序列資料執行基本分析。

1. 從 [Azure 入口網站](https://portal.azure.com/)的資源頁面按一下 URL，來移至 Azure 時間序列深入解析預覽版總管。

   ![時間序列深入解析總管 URL][19]

1. 在總管中，選取 [無上層執行個體] 節點，以查看環境中的所有 Azure 時間序列深入解析預覽版。

   ![無上層執行個體的清單][20]

1. 在所顯示的時間序列中，對第一個執行個體按一下。 然後，按一下 [顯示平均壓力]。

   ![顯示平均壓力][21]

1. 時間序列圖應該會出現在右邊：

   ![時間序列圖][22]

1. 對另外兩個時間序列重複執行**步驟 3**。 然後便可檢視所有時間序列，如下所示：

   ![所有時間序列圖][23]

1. 修改**時間範圍**可查看過去一小時的時間序列趨勢。 選取 [從] 選項方塊，如下所示：

   ![選取 [從] 選項][24]

1. 將 [從] 選項方塊內的時間變更為顯示過去一小時內的事件：

   ![選取 [從] 選項][25]

1. 然後，您可以比較過去一小時內三個裝置上的壓力：

   ![選取 [從] 選項][26]

## <a name="define-and-apply-a-model"></a>定義並套用模型

在本節中，您會套用模型以便為資料建立結構。 為了完成此模型，您會定義類型、階層和執行個體。 若要深入了解資料模型化，請移至[時間序列模型](./time-series-insights-update-tsm.md)。

1. 在總管中，選取 [模型] 索引標籤：

   ![選取 [模型] 索引標籤][27]

1. 接下來，按一下 [+ 新增] 來新增類型。 右側會開啟類型編輯器。

   ![按一下 [新增]][28]

1. 接下來，定義三個變數：類型中的壓力、溫度和溼度。 輸入下列欄位：

   | | |
   | --- | ---|
   | **名稱** | 輸入 `Chiller` |
   | **說明** | 輸入 `This is a type definition of Chiller` |

   * 現在，使用三個變數來定義壓力：

      | | |
      | --- | ---|
      | **名稱** | 輸入 `Avg Pressure` |
      | **值** | 選取 [壓力 (雙精準數)]。 請注意，在 Azure 時間序列深入解析開始接收事件後，可能需要幾分鐘的時間才會填入此欄位 |
      | **彙總作業** | 選取 `AVG` |

      ![新增變數][29]

      按一下 [+ 變數] 來新增下一個變數。

   * 現在，定義溫度：

      | | |
      | --- | ---|
      | **名稱** | 輸入 `Avg Temperature` |
      | **值** | 選取 [溫度 (雙精準數)]。 請注意，在 Azure 時間序列深入解析開始接收事件後，可能需要幾分鐘的時間才會填入此欄位 |
      | **彙總作業** | 選取 `AVG`|

      ![定義溫度][30]

   * 現在，定義溼度：

      | | |
      | --- | ---|
      | **名稱** | 輸入 `Max Humidity` |
      | **值** | 選取 [溼度 (雙精準數)]。 請注意，在 Azure 時間序列深入解析開始接收事件後，可能需要幾分鐘的時間才會填入此欄位 |
      | **彙總作業** | 選取 `MAX`|

      ![定義溫度][31]

   在定義變數後，按一下 [建立]。

1. 您會看到類型已新增：

   ![看到類型已新增][32]

1. 下一個步驟是新增階層。 在 [階層] 區段中，選取 [+ 新增] 來建立新的階層：

   ![新增階層][33]

1. 定義階層。 如下所示輸入欄位：

   | | |
   | --- | ---|
   | **名稱** | 輸入 `Location Hierarchy` |
   | **層級 1** | 輸入 `Country` |
   | **層級 2** | 輸入 `City` |
   | **層級 3** | 輸入 `Building` |

   在填寫完上述欄位之後，按一下 [建立]。

   ![定義階層][34]

1. 您會看到階層已建立：

   ![看到您的階層][35]

1. 在定義階層後，按一下左側的 [執行個體]。 在執行個體出現後，對第一個執行個體按一下，然後選取 [編輯]：

   ![編輯執行個體][36]

1. 右側會出現文字編輯器。 新增下列欄位：

   | | |
   | --- | --- |
   | **類型** | 選取 `Chiller` |
   | **說明** | 輸入 `Instance for Chiller-01.1` |
   | **階層** | 啟用 `Location Hierarchy` |
   | **國家/地區** | 輸入 `USA` |
   | **城市** | 輸入 `Seattle` |
   | **建築物** | 輸入 `Space Needle` |

    在填寫完上述欄位之後，按一下 [儲存]。

   ![儲存致冷裝置][37]

1. 針對其他感應器重複上一個步驟。 使用下列欄位：

   * 針對 Chiller 01.2：

     | | |
     | --- | --- |
     | **類型** | 選取 `Chiller` |
     | **說明** | 輸入 `Instance for Chiller-01.2` |
     | **階層** | 啟用 `Location Hierarchy` |
     | **國家/地區** | 輸入 `USA` |
     | **城市** | 輸入 `Seattle` |
     | **建築物** | 輸入 `Pacific Science Center` |

   * 針對 Chiller 01.3：

     | | |
     | --- | --- |
     | **類型** | 選取 `Chiller` |
     | **說明** | 輸入 `Instance for Chiller-01.1` |
     | **階層** | 啟用 `Location Hierarchy` |
     | **國家/地區** | 輸入 `USA` |
     | **城市** | 輸入 `New York` |
     | **建築物** | 輸入 `Empire State Building` |

1. 移至 [分析] 索引標籤，然後重新整理頁面。 展開所有階層層級，以尋找時間序列。

   ![檢視 [分析] 索引標籤][38]

1. 若要瀏覽過去一小時的時間序列，請將 [快速時間] 變更為過去一小時：

   ![瀏覽過去一小時][39]

1. 對 [太平洋科學中心] 下的時間序列按一下，然後按一下 [顯示最大溼度]。

   ![顯示最大溼度][40]

1. 隨即會開啟**最大溼度**的時間序列，間隔大小為 1 分鐘。 對某個區域按一下滑鼠左鍵即可篩選範圍。 然後，按一下滑鼠右鍵並縮放，以分析時間範圍內的事件：

   ![檢視、篩選和縮放][41]

   ![檢視、篩選和縮放][42]

1. 您也可以對某個區域按一下滑鼠左鍵，然後按一下滑鼠右鍵來查看事件的詳細資料：

   ![檢視、篩選和縮放][43]

   ![檢視、篩選和縮放][44]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：  

* 建立並使用裝置模擬加速器。
* 建立 Azure 時間序列深入解析預覽版 PAYG 環境。
* 將 Azure 時間序列深入解析預覽版環境連線至某個事件中樞。
* 執行風力發電廠模擬以將資料串流處理至 Azure 時間序列深入解析預覽版環境中。
* 執行資料的基本分析。
* 定義時間序列模型類型和階層，並將其與您的執行個體產生關聯。

現在您已經知道如何建立自己的 Azure 時間序列深入解析預覽版環境，接下來可以進一步了解Azure 時間序列深入解析中的重要概念。

了解 Azure 時間序列深入解析儲存體組態：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析預覽版儲存體和輸入](./time-series-insights-update-storage-ingress.md)

深入了解時間序列模型：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析預覽版資料模型化](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png