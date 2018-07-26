---
title: 使用 Azure 時間序列深入解析將遠端監視資料視覺化 | Microsoft Docs
description: 了解如何設定時間序列深入解析環境，來探索和分析遠端監視解決方案的時間序列資料。
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 10617c129212d8196897af750c02647f0086c8e5
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185885"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>使用時間序列深入解析將遠端監視資料視覺化

操作員可能想要進一步擴充遠端監視預先設定解決方案所提供的現成資料視覺效果。 我們的解決方案加速器原本就會提供與 TSI 的整合。 在此操作說明中，您將了解如何設定時間序列深入解析，來分析裝置遙測和偵測異常行為。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明，您需要下列各項：

* [部署遠端監視預先設定解決方案](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>建立取用者群組

您必須在要用來將資料串流處理到時間序列深入解析的 IoT 中樞，建立專屬的取用者群組。

> [!NOTE]
> 應用程式會使用取用者群組從 Azure IoT 中樞提取資料。 每個取用者群組最多允許五個輸出取用者。 您應該針對每五個輸出接收建立一個新的取用者群組，而您最多可以建立 32 個取用者群組。

1. 在 Azure 入口網站中，按一下 [Cloud Shell] 按鈕。

1. 執行下列命令以建立新的取用者群組：

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>建立新的時間序列深入解析環境

Azure 時間序列深入解析是完全受管理的分析、儲存及視覺化服務，可讓您在雲端上管理 IoT 規模的時間序列資料。 這項服務不僅提供可大幅調整的時間序列資料儲存體，還能讓您探索並分析在數秒間從世界各地流入的數十億個事件。 使用時間序列深入解析來儲存及管理以 TB 計的時間序列資料、同時探索數十億個事件並將其視覺化、執行根本原因分析，以及比對多個網站與資產。

1. 登入 [Azure 入口網站](http://portal.azure.com/)。

1. 選取 [建立資源] > [物聯網] > [時間序列深入解析]。

    ![新增時間序列深入解析](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. 若要建立時間序列深入解析環境，請使用下表中的值：

    | 設定 | 值 |
    | ------- | ----- |
    | 環境名稱 | 下列螢幕擷取畫面會使用名稱 **contorosrmtsi**。 當您完成此步驟時，請選擇您自己的唯一名稱。 |
    | 訂用帳戶 | 在下拉式清單中選取您的 Azure 訂用帳戶。 |
    | 資源群組 | **建立新項目**。 我們會使用名稱 **ContosoRM**。 |
    | 位置 | 我們使用**美國東部**。 在與您遠端監視解決方案相同的區域中建立您的環境。 |
    | SKU |**S1** |
    | Capacity | **1** |
    | 釘選到儀表板 | **是** |

    ![建立時間序列深入解析](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. 按一下頁面底部的 [新增] 。 可能需要一點時間來建立環境。

## <a name="create-event-source"></a>建立事件來源

建立新的事件來源以連線到 IoT 中樞。 確定您會使用先前步驟中所建立的取用者群組。 時間序列深入解析要求每個服務都要具備其他服務未使用的專屬取用者群組。

1. 瀏覽至新的時間序列環境。

1. 選取左側的 [事件來源]。

    ![檢視事件來源](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. 按一下 [新增] 。

    ![新增事件來源](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. 若要設定您的 IoT 中樞作為新的事件來源，請使用下表中的值：

    | 設定 | 值 |
    | ------- | ----- |
    | 事件來源名稱 | 下列螢幕擷取畫面會使用名稱 **contosorm-iot-hub**。 當您完成此步驟時，請使用您自己的唯一名稱。 |
    | 來源 | **IoT 中心** |
    | 匯入選項 | **從可用的訂用帳戶使用 IoT 中樞** |
    | 訂用帳戶識別碼 | 在下拉式清單中選取您的 Azure 訂用帳戶。 |
    | IoT 中樞名稱 | **contosorma57a6**。 從您的遠端監視解決方案使用 IoT 中樞的名稱。 |
    | IoT 中樞原則名稱 | **iothubowner** 確定所使用的原則是擁有者原則。 |
    | IoT 中樞原則金鑰 | 系統會自動填入此欄位。 |
    | IoT 中樞取用者群組 | **timeseriesinsights** |
    | 事件序列化格式 | **JSON**     | 時間戳記屬性名稱 | 保留空白 |

    ![建立事件來源](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. 按一下頁面底部的 [新增] 。

> [!NOTE]
> 如果您需要為其他使用者授與存取時間序列深入解析總管的權限，則可以使用下列步驟來[授與資料存取](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access)。

## <a name="time-series-insights-explorer"></a>時間序列深入解析總管

時間序列深入解析總管是一個 Web 應用程式，可協助您建立資料的視覺效果。

1. 選取 [概觀] 索引標籤。

1. 按一下 [移至 環境]，即會開啟時間序列深入解析總管 Web 應用程式。

    ![時間序列深入解析總管](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. 在時間選取範圍面板中，從快速時間功能表中選取 [過去 12 小時]，然後按一下 [搜尋]。

    ![時間序列深入解析總管的搜尋](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. 在左側的字詞面板中，選取 [量值] 值 **temperature** 和 [分割依據] 值 **iothub-connection-device-id**。

    ![時間序列深入解析總管的查詢](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. 以滑鼠右鍵按一下圖表，然後選取 [探索事件]。

    ![時間序列深入解析總管的事件](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. 事件會以表格式格式呈現於方格中。

    ![時間序列深入解析總管的表格](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. 按一下檢視方塊檢視按鈕。

    ![時間序列深入解析總管的檢視方塊](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. 按一下 [新增] 以在檢視方塊中建立新的查詢。

    ![時間序列深入解析總管的新增查詢](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. 選取 [過去 12 小時] 的快速時間、[量值] 的 **Humidity**，以及 [分割依據] 的 **iothub-connection-device-id**。

    ![時間序列深入解析總管的查詢](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. 按一下檢視方塊檢視按鈕，以檢視裝置計量的儀表板。

    ![時間序列深入解析總管的儀表板](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>後續步驟

若要深入了解如何在時間序列深入解析中探索和查詢資料，請參閱 [Azure 時間序列深入解析總管](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)。
