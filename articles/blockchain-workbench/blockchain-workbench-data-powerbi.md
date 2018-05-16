---
title: 在 Microsoft Power BI 中使用 Azure Blockchain Workbench 資料
description: 了解如何在 Microsoft Power BI 中載入與檢視 Azure Blockchain Workbench SQL DB 資料。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 321a34589277d62290c2fde680bb461de34b4568
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>將 Azure Blockchain Workbench 資料搭配 Microsoft Power BI 使用

Microsoft Power BI 中的功能可供使用 Power BI Desktop 輕鬆地從 SQL DB 資料庫產生強大的報告，然後將報告發佈到 [https://www.powerbi.com](http://www.powerbi.com)。

本文包含如何從 PowerBI 桌面連線至 Azure Blockchain Workbench SQL Database、建立報告，並將報告部署至 powerbi.com 的逐步解說。

## <a name="prerequisites"></a>先決條件

* 下載 [PowerBI 桌面](https://aka.ms/pbidesktopstore)。

## <a name="connecting-powerbi-to-data-in-azure-blockchain-workbench"></a>在 Azure Blockchain Workbench 將 PowerBI 連線至資料

1.  開啟 Power BI Desktop。
2.  選取 [取得資料]。

    ![取得資料](media/blockchain-workbench-data-powerbi/get-data.png)
3.  從資料來源類型中選取 [SQL Server]。

4.  在對話方塊中提供伺服器與資料庫名稱。 指定您要匯入資料，或執行 **DirectQuery**。 選取 [確定] 。

    ![選取 SQL Server](media/blockchain-workbench-data-powerbi/select-sql.png)

5.  提供存取 Azure Blockchain Workbench 的資料庫認證。 選取 [資料庫]，然後輸入您的認證。

    如果您使用的是由 Azure Blockchain Workbench 部署程序建立的認證，則使用者名稱為 **dbadmin**，且密碼為您在部署期間提供的密碼。

    ![SQL DB 設定](media/blockchain-workbench-data-powerbi/db-settings.png)

6.  一旦連線至資料庫時，[導覽列] 對話方塊會顯示資料庫內可用的資料表與檢視。 檢視是針對報告所設計，所有檢視名稱的前面都會加上 **vw**。

    ![導覽器](media/blockchain-workbench-data-powerbi/navigator.png)

7.  選取您要包含的檢視。 為了示範，我們包含 **vwContractAction**它會提供在合約上所有已發生動作的詳細資料。

    ![選取檢視](media/blockchain-workbench-data-powerbi/select-views.png)

現在您可以像平常使用 Power BI 一般地建立與發佈報告。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的資料庫檢視](blockchain-workbench-database-views.md)