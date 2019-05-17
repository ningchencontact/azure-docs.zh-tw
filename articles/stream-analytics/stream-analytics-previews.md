---
title: Azure 串流分析預覽功能
description: 本文列出目前處於預覽狀態的 Azure 串流分析功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561143"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 串流分析預覽功能

本文摘要說明目前處於預覽狀態的所有 Azure 串流分析功能。 生產環境不建議使用預覽功能。

## <a name="public-previews"></a>公開預覽

下列功能目前處於公開預覽狀態。 您目前可以利用這些功能，但請勿將其用於生產環境。

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Azure Stream Analytics （發行 2019 年） 的 visual Studio Code

Azure 串流分析作業可以在 Visual Studio Code 中撰寫。 請參閱我們[VS Code 使用者入門教學課程](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)。

### <a name="anomaly-detection"></a>異常偵測

Azure 串流分析導入了新的機器學習模型，其可支援「尖峰」和「下降」偵測，以及雙向、緩慢正向趨勢和緩慢負向趨勢偵測。 有关详细信息，请访问 [Azure 流分析中的异常情况检测](stream-analytics-machine-learning-anomaly-detection.md)。

### <a name="sql-database-reference-data"></a>SQL Database 參考資料

Azure 串流分析支援以 Azure SQL Database 作為參考資料輸入的來源。 在 Azure 入口網站和 Visual Studio 中，您可以搭配串流分析工具使用 SQL Database 作為串流分析作業的參考資料。 如需詳細資訊，請造訪[將來自 SQL Database 的參考資料用於 Azure 串流分析作業](sql-reference-data.md)。

### <a name="integration-with-azure-machine-learning"></a>與 Azure Machine Learning 整合

您可以使用 Machine Learning (ML) 函式來調整串流分析作業。 若要深入了解如何在串流分析作業中使用 ML 函式，請造訪[使用 Azure Machine Learning 函式調整串流分析作業](stream-analytics-scale-with-machine-learning-functions.md)。 透過[使用 Azure 串流分析和 Azure Machine Learning 執行情感分析](stream-analytics-machine-learning-integration-tutorial.md)來了解真實案例。

### <a name="javascript-user-defined-aggregate"></a>JavaScript 使用者定義彙總

「Azure 串流分析」支援以 JavaScript 撰寫的使用者定義彙總 (UDA)，可讓您實作複雜的具狀態商務邏輯。 若要了解如何使用 UDA，請參閱 [Azure 串流分析 JavaScript 使用者定義彙總](stream-analytics-javascript-user-defined-aggregates.md)文件。 

### <a name="live-data-testing-in-visual-studio"></a>在 Visual Studio 中即時測試資料

Azure 串流分析的 Visual Studio 工具加強了本機測試功能，讓您可以針對雲端來源 (例如，事件中樞或 IoT 中樞) 的即時事件串流查詢進行測試。 了解如何[使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 使用者定義函式

有了 .NET 標準使用者定義函式，您可以在串流管線運作期間執行 .NET Standard 程式碼。 您可以建立簡單的 C# 類別，或匯入完整的專案和程式庫。 Visual Studio 支援完整的撰寫和偵錯體驗。 如需詳細資訊，請造訪[針對 Azure 串流分析 Edge 作業開發 .NET Standard 使用者定義的函式](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="other-previews"></a>其他預覽

下列功能也可在預覽中。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#在 IoT Edge 部署和雲端 (Announced 2019 年) 的 Azure Stream Analytics 的自訂還原序列化程式

開發人員可以實作自訂的反序列化程式，在C#還原序列化 Azure Stream Analytics 收到的事件。 可以還原序列化的格式範例包括 Parquet、Protobuf、XML 或任何二進位格式。 註冊此預覽版[此處](https://aka.ms/asapreview1)。

### <a name="parquet-output-announced-may-2019"></a>Parquet 輸出 （宣布 2019 年）
Parquet 是啟用有效率的巨量資料處理的單欄式格式。 藉由輸出 Parquet 格式在 data lake 中的資料，可以利用 Azure Stream analytics power 大規模串流 ETL 和執行批次處理、 定型機器學習服務演算法或上將歷程記錄資料執行互動式查詢。 註冊此預覽版[此處](https://aka.ms/asapreview1)。

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>與事件中樞 (Announced 2019 年) 的一種單鍵整合 
透過這項整合，您現在可以將內送的資料視覺化，並開始撰寫一個按一下 Stream Analytics 查詢，從事件中樞入口網站。 準備您的查詢之後，您將能夠內容中按幾下滑鼠，並開始取得即時深入解析。 這會大幅降低開發即時分析解決方案的成本與時間。 註冊此預覽版[此處](https://aka.ms/asapreview1)。

### <a name="support-for-azure-stack-announced-may-2019"></a>適用於 Azure Stack （宣布 2019 年） 的支援
Azure IoT Edge 執行階段上啟用這項功能會利用自訂的 Azure Stack 功能，例如本機輸入的原生支援，並將輸出 （例如事件中樞，IoT 中樞、 Blob 儲存體） 的 Azure Stack 上執行。 這項新整合可讓您建置可以分析您的資料產生的位置、 降低延遲，並最大化 insights 接近的混合式架構。
註冊此預覽版[此處](https://aka.ms/asapreview1)。

