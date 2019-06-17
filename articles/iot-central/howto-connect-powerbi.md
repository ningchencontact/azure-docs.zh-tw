---
title: 視覺化 Power BI 儀表板中的 Azure IoT Central 資料 | Microsoft Docs
description: 使用 Azure IoT Central 的 Power BI 解決方案，將 IoT Central 資料 視覺化並加以分析。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: 16d7691d59e0805c7f8cf565a599ca5e6a78a7cf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053038"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>視覺化與分析 Power BI 儀表板中的 Azure IoT Central 資料

*此主題適用於系統管理員。*

![Power BI 解決方案管線](media/howto-connect-powerbi/iot-continuous-data-export.png)

您可以使用 Azure IoT Central 的 Power BI 解決方案建立功能強大的 Power BI 儀表板，以監視 IoT 裝置的效能。 在 Power BI 儀表板中，您可以：
- 追蹤裝置在一段時間內傳送的資料量
- 在遙測、狀態和事件之間比較資料量
- 識別報告許多量測的裝置
- 觀察裝置量測的歷程記錄趨勢
- 識別傳送許多重大事件的問題裝置

此解決方案會設定管道，以便從[連續資料匯出](howto-export-data.md)中取得 Azure Blob 儲存體帳戶中的資料。 此資料會流至 Azure Functions、Azure Data Factory 和 Azure SQL Database，以便處理和轉換資料。 此輸出可在 Power BI 報告中進行視覺化與分析，您可以 PBIX 檔案格式下載該報告。 所有這些資源都會建立在您的 Azure 訂用帳戶中，因此您可以根據需求來自訂每個元件。

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>從 Microsoft AppSource取得 [Azure IoT Central 的 Power BI 解決方案](https://aka.ms/iotcentralpowerbisolutiontemplate)。

## <a name="prerequisites"></a>必要條件
設定解決方案需要下列項目：
- Azure 訂用帳戶的存取權
- 使用[連續資料匯出](howto-export-data.md)從 IoT Central 應用程式匯出的資料。 我們建議您開啟量測、裝置和裝置範本串流，以充分利用 Power BI 儀表板。
- Power BI Desktop (最新版)
- Power BI Pro (如果您想與其他人共用儀表板)

## <a name="reports"></a>報告

系統會自動產生兩份報告。 

第一份報告會顯示裝置所報告的量測歷程記錄檢視，並細分不同類型的量測，以及已送出最多量測的裝置。

![Power BI 報告第一頁](media/howto-connect-powerbi/template-page1-hasdata.PNG)

第二份報告會深入探討到事件，並顯示錯誤和警告報告的歷程記錄檢視。 其中也會顯示哪些裝置報告的事件總數最多，尤其是錯誤事件和警告事件。

![Power BI 報告第二頁](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>架構
可以在 Azure 入口網站中存取所有已建立的資源。 所有資訊應該屬於一個資源群組。

![資源群組的 Azure 入口網站檢視](media/howto-connect-powerbi/azure-deployment.PNG)

每個資源及其使用方式的詳細資訊如下所述。

### <a name="azure-functions"></a>Azure Functions
每當新的檔案寫入至 Blob 儲存體時，會觸發 Azure 函式應用程式。 函式會擷取每個度量、裝置和裝置範本檔案中的欄位，並於其中填入 Azure Data Factory 要使用的多個中繼 SQL 資料表。

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory 會連線到 SQL 資料庫成為連結的服務， 其會執行預存程序活動來處理資料，並將資料儲存在分析資料表中。

### <a name="azure-sql-database"></a>Azure SQL Database
會自動建立這些資料表來填入預設報表。 請多加研究 Power BI 中的這類結構描述，您可以針對該資料建立屬於自己的視覺效果。

| 資料表名稱 |
|------------|
|[analytics].[Measurements]|
|[analytics].[Messages]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>預估成本

以下是相關 Azure 成本的估計 (Azure Function、Data Factory、Azure SQL)。 所有價格均以美元計價。 請記住，價格地區而異，請務必查詢各項服務的最新價格，以取得實際價格。
範本預設為下列預設值 (您可以在設定後修改其中任何項目)：

- Azure Functions：App Service 方案 S1 每月 74.40 美元
- Azure SQL S1，每月約 30 美元

建議您自行熟悉多種定價選項並進行調整，以符合您的需求。

## <a name="resources"></a>資源

造訪 AppSource 取得 [Azure IoT Central 的 Power BI 解決方案](https://aka.ms/iotcentralpowerbisolutiontemplate)。

## <a name="next-steps"></a>後續步驟

現在您已了解如何將 Power BI 中的資料視覺化，以下是建議執行的下一個步驟：

> [!div class="nextstepaction"]
> [如何管理裝置](howto-manage-devices.md)