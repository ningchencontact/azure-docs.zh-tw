---
title: 什麼是 Azure SQL Database Edge？ | Microsoft Docs
description: 深入瞭解 Azure SQL Database Edge
keywords: sql database edge 簡介，什麼是 sql database edge，sql database edge 總覽
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9cbfc17e7412b4d30f082354996721ee7b5d6d5b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514000"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>什麼是 Azure SQL Database Edge 預覽版？

Azure SQL Database Edge 預覽是適用于 IoT 和 IoT Edge 部署的優化關係資料庫引擎。 它提供的功能可為 IoT 應用程式和解決方案建立高效能的資料儲存和處理層。 Azure SQL Database Edge 提供串流、處理和分析關聯式和非關聯式的功能，例如 JSON、圖形和時間序列資料，這可讓它成為各種新式 IoT 應用程式的正確選擇。

Azure SQL Database Edge 是以最新版本的[Microsoft SQL Server 資料庫引擎](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)為基礎，可提供領先業界的效能、安全性和查詢處理功能。 從開始，Azure SQL Database 邊緣是以與 SQL Server 和 Azure SQL Database 相同的引擎為基礎，它提供了相同的 T-sql 程式設計介面區，讓應用程式或解決方案的開發變得更輕鬆且更快速，同時讓應用程式IoT Edge 裝置、資料中心和雲端之間的可攜性。

## <a name="deployment-models"></a>部署模型

Azure SQL Database Edge 適用于 Azure Marketplace，而且可以部署為[Azure IoT Edge](../iot-edge/about-iot-edge.md)的模組。 如需詳細資訊，請參閱[部署 Azure SQL Database Edge](deploy-portal.md)。<br>

![SQL Database Edge 總覽圖表](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL Database Edge 的版本

SQL Database Edge 提供三種不同的版本或軟體方案。 這些版本具有相同的功能集，而且只有其許可權和支援的 cpu/記憶體數量不同。

   |**規劃**  |**說明**  |
   |---------|---------|
   |開發人員  |  僅限開發 sku，具有與下面所述標準 sku 強加的相同節流限制 |
   |標準   |  標準方案最多可支援4個 CPU，最高可達 32 GB 的記憶體供 SQL Database Edge 容器之用。 |
   |高級    |  Premium sku 最多可支援8個核心，最多可達 64 GB 的記憶體供 SQL Database Edge 容器之用。 |

## <a name="pricing-and-availability"></a>定價和可用性

Azure SQL Database 目前為預覽狀態。 如需定價和可用性的詳細資訊，請參閱[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)。

> [!IMPORTANT]
> 若要瞭解 Azure SQL Database 邊緣和 SQL Server 之間的功能差異，以及不同 Azure SQL Database 邊緣選項之間的差異，請參閱[SQL Database Edge 資料庫功能](https://azure.microsoft.com/services/sql-database-edge/)。

## <a name="streaming-capabilities"></a>串流功能  

Azure SQL Database Edge 會提供內建串流功能，以進行即時分析和複雜的事件處理。 串流功能是使用與[Azure 串流分析](../stream-analytics/stream-analytics-introduction.md)相同的結構所建立，並提供與[Azure IoT Edge 串流分析](../stream-analytics/stream-analytics-edge.md)類似的功能。

適用于 Azure SQL Database Edge 的串流處理引擎是專為低延遲、復原、有效率的頻寬和合規性使用所設計。

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning 和人工智慧功能

Azure SQL Database Edge 藉由整合 open format ONNX （Open Neural Network Exchange）執行時間來提供內建的機器學習和分析功能，讓您可以在不同的架構之間交換深度學習和類神經網路模型。 如需 ONNX 的詳細資訊，請參閱[這裡](https://onnx.ai/)。 ONNX 執行時間提供以您選擇的語言或工具開發模型的彈性，然後可以轉換成 ONNX 格式，以便在 SQL Database 邊緣內執行。 如需詳細資訊，請參閱[使用 SQL Database Edge 中的 ONNX Machine Learning 和人工智慧](onnx-overview.md)。

## <a name="working-with-azure-sql-database-edge"></a>使用 Azure SQL Database 邊緣

Azure SQL Database Edge 讓開發和維護應用程式變得更容易且更具生產力。 使用者可以使用所有熟悉的工具和技術，針對其 IoT Edge 需求建立絕佳的應用程式和解決方案。 使用者可以使用下列工具，在 SQL Database Edge 中進行開發：

- [Azure 入口網站](https://portal.azure.com/)-用於管理所有 Azure 服務的 web 應用程式。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) -免費、可下載的用戶端應用程式，可用於管理任何 SQL 基礎結構，從 SQL Server 到 SQL Database。
- [SQL Server Data Tools Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) -可供下載的免費用戶端應用程式，可用於開發 SQL Server 關係資料庫、SQL 資料庫、Integration Services 封裝、Analysis Services 資料模型和 Reporting Services 報表。
- [Azure Data Studio](/sql/azure-data-studio/what-is/) -免費、可下載的跨平臺資料庫工具，適用于使用 Windows、MacOS 和 Linux 上的 Microsoft 系列內部部署和雲端資料平臺的資料專業人員。
- [Visual Studio Code](https://code.visualstudio.com/docs) -適用于 Windows、MacOS 和 Linux 的免費、可下載、開放原始碼程式碼編輯器。 它支援延伸模組，包括用於查詢 Microsoft SQL Server、Azure SQL Database 和 Azure SQL 資料倉儲的[mssql 擴充](https://aka.ms/mssql-marketplace)功能。


## <a name="next-steps"></a>後續步驟

- 如需價格與可用性的相關詳細資料，請參閱[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)。
- 要求為您的訂用帳戶啟用 Azure SQL Database Edge。
- 若要開始使用，請參閱下列各項：
  - [透過 Azure 入口網站部署 SQL Database Edge](deploy-portal.md)
  - [SQL Database Edge 的 Machine Learning 和人工智慧](onnx-overview.md)
