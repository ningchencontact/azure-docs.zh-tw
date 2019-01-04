---
title: Azure 串流分析預覽功能
description: 本文列出目前處於預覽狀態的 Azure 串流分析功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 9b721ab614bf1797604fe342de117c78ba703f96
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557592"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 串流分析預覽功能

本文摘要說明目前處於預覽狀態的所有 Azure 串流分析功能。 生產環境不建議使用預覽功能。

## <a name="public-previews"></a>公開預覽

下列功能目前處於公開預覽狀態。 您目前可以利用這些功能，但請勿將其用於生產環境。

### <a name="integration-with-azure-machine-learning"></a>與 Azure Machine Learning 整合

您可以使用 Machine Learning (ML) 函式來調整串流分析作業。 若要深入了解如何在串流分析作業中使用 ML 函式，請造訪[使用 Azure Machine Learning 函式調整串流分析作業](stream-analytics-scale-with-machine-learning-functions.md)。 透過[使用 Azure 串流分析和 Azure Machine Learning 執行情感分析](stream-analytics-machine-learning-integration-tutorial.md)來了解真實案例。

### <a name="session-windows"></a>工作階段時間範圍

串流分析具備對時間範圍函式的原生支援，可讓開發人員輕鬆地撰寫複雜的串流處理工作。 [工作階段時間範圍](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics)會將相近時間送達的事件分組，並將沒有任何資料的時間範圍篩選掉。 若要深入了解時間範圍函式，請造訪[串流分析時間範圍函式簡介](stream-analytics-window-functions.md)。

### <a name="blob-output-partitioning-by-custom-time"></a>透過自訂時間來分割 Blob 輸出

Azure 串流分析可以根據自訂時間屬性來輸出至 Blob 儲存體。 如需詳細資訊，請造訪 [Azure 串流分析 Blob 儲存體輸出的自訂日期時間路徑模式](stream-analytics-custom-path-patterns-blob-storage-output.md)。

### <a name="javascript-user-defined-aggregate"></a>JavaScript 使用者定義彙總

「Azure 串流分析」支援以 JavaScript 撰寫的使用者定義彙總 (UDA)，可讓您實作複雜的具狀態商務邏輯。 若要了解如何使用 UDA，請參閱 [Azure 串流分析 JavaScript 使用者定義彙總](stream-analytics-javascript-user-defined-aggregates.md)文件。 

### <a name="live-data-testing-in-visual-studio"></a>在 Visual Studio 中即時測試資料

Azure 串流分析的 Visual Studio 工具加強了本機測試功能，讓您可以針對雲端來源 (例如，事件中樞或 IoT 中樞) 的即時事件串流查詢進行測試。 了解如何[使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 使用者定義函式

有了 .NET 標準使用者定義函式，您可以在串流管線運作期間執行 .NET Standard 程式碼。 您可以建立簡單的 C# 類別，或匯入完整的專案和程式庫。 Visual Studio 支援完整的撰寫和偵錯體驗。 如需詳細資訊，請造訪[針對 Azure 串流分析 Edge 作業開發 .NET Standard 使用者定義的函式](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="private-previews"></a>個人預覽版

下列功能目前處於個人預覽狀態。 若要存取這些預覽功能，請造訪 Azure 串流分析個人預覽版[註冊](https://aka.ms/ASApreview1)頁面。

### <a name="anomaly-detection"></a>異常偵測

Azure 串流分析導入了新的機器學習模型，其可支援「尖峰」和「下降」偵測，以及雙向、緩慢正向趨勢和緩慢負向趨勢偵測。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Azure IoT Edge 串流分析的 C# 自訂 Deserializer

開發人員現在可以在 C# 實作自訂 Deserializer，以將 Azure 串流分析所收到的事件還原序列化。 可以還原序列化的格式範例包括 Parquet、Protobuf、XML 或任何二進位格式。

### <a name="blob-output-partitioning-by-custom-attribute"></a>透過自訂屬性來分割 Blob 輸出

您現在可以根據查詢中的任何資料行，來分割 Azure 串流分析對 Blob 儲存體的輸出。

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Azure Data Lake Storage 的 Azure 資源驗證受控識別

您現在可以在寫入至 Azure Data Lake Storage Gen1 時，使用 Azure 資源型驗證的受控識別將即時管線運作化，從而讓您以程式設計方式建立作業。 如需詳細資訊，請造訪[使用 Azure 資源的受控識別來向 Azure Data Lake Storage Gen1 輸出驗證 Azure 串流分析作業](stream-analytics-managed-identities-adls.md)。

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析中的八個新功能](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Azure 串流分析現已推出四個新功能](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
