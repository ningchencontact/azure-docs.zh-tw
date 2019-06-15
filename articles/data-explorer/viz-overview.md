---
title: Azure 的資料總管資料視覺效果
description: 深入了解不同的方式，您可以將您的 Azure 資料總管資料視覺化
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481830"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 資料總管的資料視覺效果 

Azure 的資料總管是用來建立複雜的分析解決方案，以大量資料的記錄檔和遙測資料的快速又可高度擴充的資料探勘服務。 Azure 資料總管整合各種視覺效果工具，讓您可以將資料視覺化，並跨組織共用結果。 這項資料可以轉換成可操作的見解，以對您的業務的影響。

資料視覺效果和報表是資料分析程序的重要步驟。 Azure 的資料總管支援許多 BI 服務，因此您可以使用最適合您的案例和預算。

* Azure 的資料總管視覺效果：使用 Kusto 查詢語言[ `render operator` ](/azure/kusto/query/renderoperator)提供各種不同的視覺效果類型，以描述查詢結果。 查詢的視覺效果很有幫助異常偵測和預測、 機器學習服務和更多功能。

* [Power BI](https://powerbi.microsoft.com)：Azure 的資料總管會提供能夠連線到 Power BI 使用不同的方法： 

  * [內建的原生 Power BI 連接器](/azure/data-explorer/power-bi-connector)

  * [在資料總管中 Azure 匯入 Power BI 的查詢匯入](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL 查詢](/azure/data-explorer/power-bi-sql-query)。

* [Microsoft Excel](https://products.office.com/excel):Azure 的資料總管會提供連接到 Excel 中使用內建的原生 Excel 連接器，或從 Azure 資料總管將查詢匯入到 Excel 的功能。

* [Grafana](https://grafana.com):Grafana 提供 Azure 資料總管外掛程式，可讓您將來自 Azure 資料總管資料視覺化。 您[設定 Azure 資料總管做為資料來源之 Grafana，並再將資料視覺化](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com):Azure 的資料總管將提供使用 JDBC 連接器能夠連線到 Sisense。 您[設定 Azure 資料總管做為資料來源之 Sisense，並再將資料視覺化](/azure/data-explorer/sisense)。

* [Tableau](https://www.tableau.com):Azure 資料總管提供的功能，來連接使用 Tableau[的 ODBC 連接器在 Tableau 中的資料並加以視覺化](/azure/data-explorer/connect-odbc)。

* [Qlik](https://www.qlik.com):Azure 的資料檔案總管提供的功能，來連接使用 Qlik[的 ODBC 連接器](/azure/data-explorer/connect-odbc)。