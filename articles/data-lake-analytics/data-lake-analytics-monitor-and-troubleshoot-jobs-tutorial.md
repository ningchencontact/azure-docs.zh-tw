---
title: 使用 Azure 入口網站監視 Azure Data Lake Analytics 中的作業 | Microsoft Docs
description: '了解如何使用 Azure 入口網站疑難排解資料湖分析作業。 '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 14b1f4ec9dff78e4b5d2480755a4b1f2579ec135
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
ms.locfileid: "33884774"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>使用 Azure 入口網站監視 Azure Data Lake Analytics 中的作業

**查看所有工作**

1. 在 Azure 入口網站中，按一下左上角的 [ **Microsoft Azure** ]。
2. 按一下具有您資料湖分析帳戶名稱的磚。  工作摘要隨即顯示在 [ **工作管理** ] 磚上。

    ![Azure 資料湖分析作業管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    [工作管理可] 讓您對工作的狀態一目瞭然。 請注意失敗的工作。
3. 按一下 [ **工作管理** ] 磚以檢視工作。 工作分類成 [執行中]、[已排入序列] 和 [結束]。 您應該會在 [ **結束** ] 區段中看到失敗的工作。 該工作應該列在清單中的首位。 如果有很多工作，您可以按一下 [ **篩選** ] 來協助您找出工作。

    ![Azure 資料湖分析篩選作業](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. 按一下清單中的失敗工作，以開啟工作詳細資料：

    ![Azure 資料湖分析失敗作業](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    請注意 [ **重新提交** ] 按鈕。 修正問題之後，您可以重新提交工作。
5. 按一下前一個螢幕擷取畫面中反白顯示的部分，開啟錯誤詳細資料。  您會看到類似下面的畫面：

    ![Azure 資料湖分析失敗作業詳細資料](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    它會告訴您找不到來源資料夾。
6. 按一下 [ **重複的指令碼**]。
7. 將 **FROM** 路徑更新為：

    "/Samples/Data/SearchLog.tsv"
8. 按一下 [ **提交作業**]。

## <a name="see-also"></a>另請參閱
* [Azure 資料湖分析概觀](data-lake-analytics-overview.md)
* [使用 Azure PowerShell 開始使用 Azure 資料湖分析](data-lake-analytics-get-started-powershell.md)
* [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
