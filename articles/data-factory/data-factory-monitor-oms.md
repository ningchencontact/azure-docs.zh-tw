---
title: 使用 OMS 監視 Azure Data Factory | Microsoft Docs
description: 了解如何將資料路由傳送至 Operations Management Suite (OMS) 進行分析，進而監視 Azure Data Factory。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303979"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>使用 Operations Management Suite (OMS) 監視 Azure Data Factory

您可以使用 Azure Data Factory 與 Azure 監視器的整合，將資料路由傳送至 Operations Management Suite (OMS)。 此整合在下列案例中很實用 ：

1.  您想要對由 Data Factory 發佈至 OMS 的一組豐富計量，撰寫複雜的查詢。 您也可以透過 OMS 建立這些查詢的自訂警示。

2.  您想要監視所有資料處理站。 您可以將資料從多個資料處理站傳送至單一 OMS 工作區。

## <a name="get-started"></a>開始使用

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>設定診斷設定和 OMS 工作區

啟用資料處理站的 [診斷設定]。

1.  選取 [Azure 監視器] -> [診斷設定] -> 選取資料處理站 -> 開啟診斷。

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  提供診斷設定，包括 OMS 工作區的組態。

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>從 Azure Marketplace 安裝 Azure Data Factory Analytics OMS 套件

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

按一下 [建立]，然後選取 OMS 工作區和 OMS 工作區設定。

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>使用 OMS 監視 Azure Data Factory 計量

安裝 **Azure Data Factory Analytics** OMS 套件，可建立能啟用下列計量的預設檢視集：

- ADF 執行- 1) 依 Data Factory 的管線執行

- ADF 執行- 2) 依 Data Factory 的活動執行

- ADF 執行- 3) 依 Data Factory 的觸發程序執行

- ADF 錯誤- 1) 依 Data Factory 的前 10 個管線錯誤

- ADF 錯誤- 2) 依 Data Factory 的前 10 個活動執行

- ADF 錯誤- 3) 依 Data Factory 的前 10 個觸發程序執行

- ADF 統計資料- 1) 依類型的活動執行

- ADF 統計資料- 2) 依類型的觸發程序執行

- ADF 統計資料- 3) 最大管線執行持續時間

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

您可以將上述計量視覺化、查看這些計量背後的查詢、編輯查詢、建立警示等等。

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>後續步驟

請參閱[以程式設計方式監視和管理管線](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically)，以了解如何藉由執行指令碼來監視和管理管線。
