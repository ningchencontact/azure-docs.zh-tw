---
title: 安裝適用於 SQL 資料倉儲的 Visual Studio 和 SSDT | Microsoft Docs
description: 安裝適用於 Azure SQL 資料倉儲的 Visual Studio 和 SQL Server Development Tools (SSDT)
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa904ac62ce41cc89945ddfdef2e426143e42bb4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479485"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>安裝適用於 SQL 資料倉儲的 Visual Studio 和 SSDT
使用 Visual Studio 2019 開發 SQL 資料倉儲的應用程式。 SQL 資料倉儲目前不支援 Visual Studio 2019 SSDT。 

搭配使用 Visual Studio 與 SSDT, 可讓您使用 SQL Server 物件總管, 以視覺化方式流覽 SQL 資料倉儲中的資料表、視圖、預存程式和更多物件。 它也可讓您執行查詢。

> [!NOTE]
> SQL 資料倉儲尚未支援 Visual Studio 資料庫專案。 若要定期接收這項功能的更新，請在 [UserVoice] 投票。
> 
> 

## <a name="step-1-install-visual-studio"></a>步驟 1:安裝 Visual Studio
遵循下列連結來下載並安裝 Visual Studio。 如果已安裝 Visual Studio 2013 或更新版本，可以跳至步驟 2 安裝 SSDT。

1. [下載 Visual Studio][]。
2. 依照 MSDN 上的 [安裝 Visual Studio][Installing Visual Studio] 指南並選擇預設組態。

## <a name="step-2-install-ssdt"></a>步驟 2:安裝 SSDT
若要安裝適用於 Visual Studio 的 SSDT，請先依照下列步驟，從 Visual Studio 內檢查 SSDT 更新。

1. 在 Visual Studio 中，按一下 [工具] / [擴充功能和更新] / [更新]。
2. 選取 [產品更新]，然後尋找 [適用於資料庫工具的 Microsoft SQL Server 更新]

如果找不到更新, 您應該安裝最新版本。 若要確認已安裝 SSDT，請按一下 [說明] / [關於 Microsoft Visual Studio]，然後在清單中尋找 SQL Server Data Tools。 如果 Visual Studio 無法使用安裝選項, 您可以流覽[SSDT 下載][SSDT Download]頁面, 手動下載並安裝 SSDT。

## <a name="next-steps"></a>後續步驟
既然您已有最新版本的 SSDT，就可以開始[連線][connect]到您的 SQL 資料倉儲。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[下載 Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
