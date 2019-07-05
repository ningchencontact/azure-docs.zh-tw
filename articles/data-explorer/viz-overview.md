---
title: Azure 的資料總管資料視覺效果
description: 深入了解不同的方式，您可以將您的 Azure 資料總管資料視覺化
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536732"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 資料總管的資料視覺效果 

Azure 的資料總管是用來建立複雜的分析解決方案，以大量資料的記錄檔和遙測資料的快速又可高度擴充的資料探勘服務。 Azure 資料總管整合各種視覺效果工具，讓您可以將資料視覺化，並跨組織共用結果。 這項資料可以轉換成可操作的見解，以對您的業務的影響。

資料視覺效果和報表是資料分析程序的重要步驟。 Azure 的資料總管支援許多 BI 服務，因此您可以使用最適合您的案例和預算。

## <a name="kusto-query-language-visualizations"></a>Kusto 查詢語言的視覺效果

Kusto 查詢語言[ `render operator` ](/azure/kusto/query/renderoperator)提供各種不同的視覺效果，例如資料表、 圓形圖和橫條圖來描述查詢結果。 查詢的視覺效果很有幫助異常偵測和預測、 機器學習服務和更多功能。

## <a name="power-bi"></a>Power BI

Azure 的資料總管能夠讓您連接到[Power BI](https://powerbi.microsoft.com)使用各種方法： 

  * [內建的原生 Power BI 連接器](/azure/data-explorer/power-bi-connector)

  * [在資料總管中 Azure 匯入 Power BI 的查詢匯入](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 的資料總管能夠讓您連接到[Microsoft Excel](https://products.office.com/excel)使用內建的原生 Excel 連接器，或從 Azure 資料總管將查詢匯入到 Excel。

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com)提供 Azure 資料總管外掛程式，可讓您將來自 Azure 資料總管資料視覺化。 您[設定 Azure 資料總管做為資料來源之 Grafana，並再將資料視覺化](/azure/data-explorer/grafana)。 

## <a name="odbc-connector"></a>ODBC 連接器

提供 azure 資料總管[開放式資料庫連接 (ODBC) 連接器](connect-odbc.md)讓任何支援 ODBC 應用程式可以連線到 Azure 資料總管。

## <a name="tableau"></a>Tableau

Azure 的資料總管能夠讓您連接到[Tableau](https://www.tableau.com)使用[ODBC 連接器](/azure/data-explorer/connect-odbc)，然後[Tableau 的資料視覺化](tableau.md)。

## <a name="qlik"></a>Qlik

Azure 的資料總管能夠讓您連接到[Qlik](https://www.qlik.com)使用[ODBC 連接器](/azure/data-explorer/connect-odbc)然後建立 Qlik Sense 儀表板，並將資料視覺化。 使用下列影片，您可以了解如何使用 Qlik Azure 資料總管資料視覺化。 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 的資料總管能夠讓您連接到[Sisense](https://www.sisense.com)使用 JDBC 連接器。 您[設定 Azure 資料總管做為資料來源之 Sisense，並再將資料視覺化](/azure/data-explorer/sisense)。