---
title: Azure 時間序列深入解析 (預覽) 教學課程 | Microsoft Docs
description: 了解 Azure 時間序列深入解析 (預覽)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872300"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Azure 時間序列深入解析 (預覽) 教學課程

本教學課程將逐步引導您建立已從模擬裝置填入資料的 Azure 時間序列深入解析 (TSI) 預覽環境。 在本教學課程中，您了解如何：

* 建立 TSI (預覽) 環境。
* 將 TSI (預覽) 環境連線到事件中樞。
* 執行風力發電廠模擬以將資料串流處理至 TSI 預覽環境中。
* 在資料上執行基本的分析。
* 定義時間序列模型類型和階層，並將其與您的執行個體產生關聯。

## <a name="create-a-time-series-insights-preview-environment"></a>建立時間序列深入解析 (預覽) 環境

本節將說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立 Azure TSI (預覽) 環境。

1. 使用您的訂用帳戶登入 Azure 入口網站
1. 選取左上方的 [+ 建立資源]。
1. 選取 [物聯網] 類別，然後選取 [時間序列深入解析]。

  ![tutorial-one][1]

1. 在 [時間序列深入解析環境] 頁面上填入必要參數，然後按一下 [下一步：事件來源]

  ![tutorial-two][2]

1. 在 [事件來源] **** 頁面中上填入必要參數，然後按一下 [檢閱 + 建立]。

  ![tutorial-three][3]

1. 檢閱所有詳細資料，然後按一下 [建立] 來開始佈建您的環境。

  ![tutorial-four][4]

1. 部署順利完成後，您會收到通知。

  ![tutorial-five][5]

## <a name="send-events-to-your-tsi-environment"></a>將事件傳送至 TSI 環境

在本節中，您將使用風車裝置模擬器，透過事件中樞將事件傳送至 TSI 環境。

  1. 在 Azure 入口網站中，瀏覽至您的事件中樞資源，並將其連線至 TSI 環境。 了解[如何將您的資源連線至現有事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

  1. 在 [事件中樞資源] 頁面上，依序移至 [共用存取原則] 和 [RootManageSharedAccessKey]。 複製此處顯示的**連接字串 – 主要金鑰** 。

      ![tutorial-six][6]

  1. 移至 [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html)。 此 Web 應用程式會模擬風車裝置。
  1. 將從步驟 3 複製的連接字串貼到 [事件中樞連接字串] 中

      ![tutorial-seven][7]

  1. 按一下 [點擊即開始] 來將事件推送至事件中樞。 在此階段上，名為 `instances.json` 的檔案會下載到您的機器中。 請儲存此檔案，因為我們稍後會用到。

  1. 回到事件中樞。 您現在應該會看到 hub.d 所接收的新事件

     ![tutorial-eight][8]

## <a name="analyze-data-in-your-environment"></a>在您的環境中分析資料

在本節中，您將使用時間序列深入解析的更新總管，對時間序列資料執行基本分析。

  1. 從 Azure 入口網站的資源頁面中按一下 URL，並瀏覽至您的時間序列深入解析更新總管。

      ![tutorial-nine][9]

  1. 在總管中，按一下 [無上層執行個體] 節點，以查看環境中的所有時間序列執行個體。

     ![tutorial-ten][10]

  1. 在本教學課程中，我們要分析前一天傳送的資料。 若要這樣做，請按一下 [快速時間]，然後選取 [過去 24 小時] 選項。

     ![tutorial-eleven][11]

  1. 選取 [Sensor_0]，然後選擇 [顯示平均值]，即可將此時間序列執行個體送出的資料視覺化。

     ![tutorial-twelve][12]

  1. 同樣地，您可以繪製來自其他時間序列執行個體的資料，以執行基本分析。

     ![tutorial-thirteen][13]

## <a name="define-a-type--hierarchy"></a>定義類別和階層 

在本節中，您將撰寫類型和階層，並讓這些項目與您的時間序列執行個體產生關聯。 深入了解[時間序列模型](./time-series-insights-update-tsm.md)。

  1. 在總管中，按一下應用程式列中的 [模型] 索引標籤。

     ![tutorial-fourteen][14]

  1. 在 [類型] 區段中，按一下 [+ 新增]。 這可讓您建立新的時間序列模型類型。

     ![tutorial-fifteen][15]

  1. 在類型編輯器中，輸入**名稱**和**描述**，然後建立**平均值**、**最小值**和**最大值**的變數，如下所示。 按一下 [建立] 以儲存類型。

     ![tutorial-sixteen][16]

     ![tutorial-seventeen][17]

  1. 在 [階層] 區段中，按一下 [+ 新增]。 這可讓您建立新的時間序列模型階層。

     ![tutorial-eighteen][18]

  1. 在階層編輯器中，輸入**名稱**及新增階層層級，如下所示。 按一下 [建立] 以儲存階層。

     ![tutorial-nineteen][19]

     ![tutorial-twenty][20]

  1. 在 [執行個體] 區段中選取執行個體，然後按一下 [編輯]。 這可讓您將類型和階層與此執行個體產生關聯。

     ![tutorial-twenty-one][21]

  1. 在執行個體編輯器中，選擇上述步驟 3 和 5 中定義的類型與階層，如圖所示。

     ![tutorial-twenty-two][22]

  1. 或者，若要一次對所有執行個體這麼做，您可以編輯稍早下載的 `instances.json` 檔案。 在此檔案中，將所有 **typeId** 和 **hierarchyId** 欄位取代為從上述步驟 3 和 5 取得的識別碼。

  1. 在 [執行個體] 區段中，按一下 [上傳 JSON] 並上傳已編輯的 `instances.json` 檔案，如下所示。

     ![tutorial-twenty-three][23]

  1. 瀏覽至 [分析] 索引標籤，然後重新整理瀏覽器。 現在，您應該會看到與上方所定義類型和階層相關聯的所有執行個體。

     ![tutorial-twenty-four][24]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：  

* 建立 TSI (預覽) 環境。
* 將 TSI (預覽) 環境連線到事件中樞。
* 執行風力發電廠模擬以將資料串流處理至 TSI (預覽) 環境中。
* 在資料上執行基本的分析。
* 定義時間序列模型類型和階層，並將其與您的執行個體產生關聯。

現在您已經知道如何建立自己的 TSI 更新環境，接下來可以進一步了解 TSI 中的重要概念。

了解 TSI 儲存體組態：

> [!div class="nextstepaction"]
> [Azure TSI (預覽) 儲存體和輸入](./time-series-insights-update-storage-ingress.md)

深入了解時間序列模型：

> [!div class="nextstepaction"]
> [Azure TSI (預覽) 資料模型](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png