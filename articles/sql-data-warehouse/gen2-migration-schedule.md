---
title: 將現有 Azure SQL 資料倉儲遷移至 Gen2 | Microsoft Docs
description: 將現有資料倉儲遷移至 Gen 2 的指示和各區域的移轉排程。
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.openlocfilehash: d4724672510d6ccbbc819691d621400cb00d8c9a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405450"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>將您的資料倉儲升級為 Gen2

Microsoft 正在協助降低執行資料倉儲的進入層級成本。  較低的計算層能夠處理需求最高的查詢, 現已可供 Azure SQL 資料倉儲。 閱讀 Gen2 的完整公告[較低的計算層支援](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)。 新的供應專案適用于下表所述的區域。 在支援的區域中，現有 Gen1 資料倉儲可以透過下列方式升級至 Gen2：

- **自動升級程序：** 在區域中提供服務時, 自動升級不會立即啟動。  在特定區域中啟動自動升級時，個別的 DW 升級會在您選取的維護排程期間進行。
- [**自我升級至 Gen2:** ](#self-upgrade-to-gen2)您可以執行自我升級至 Gen2, 以控制升級的時機。 如果您的區域尚不受支援, 您可以從還原點直接還原至支援區域中的 Gen2 實例。

## <a name="automated-schedule-and-region-availability-table"></a>自動化排程和國家可用性表格

下表依區域彙整較低 Gen2 計算層的可用時間和自動升級的開始時間。 日後有可能會變更。 回頭查看您的區域何時可用。

\* 表示目前無法使用該區域的特定排程。

| **區域** | **可用的較低 Gen2** | **自動升級開始** |
|:--- |:--- |:--- |
| 澳大利亞東部 |可用 |完成 |
| 澳大利亞東南部 |可用 |完成 |
| 巴西南部 |可用 |完成 |
| 加拿大中部 |可用 |完成 |
| 加拿大東部 |2020年6月1日 |2020年7月1日 |
| 美國中部 |可用 |完成 |
| 中國東部 |\* |\* |
| 中國東部 2 |可用 |完成 |
| 中國北部 |\* |\* |
| 中國北部 2 |可用 |完成 |
| 東亞 |可用 |完成 |
| East US |可用 |完成 |
| 美國東部 2 |可用 |完成 |
| 法國中部 |可用 |進行中 |
| 德國中部 |\* |\* |
| 德國中西部 |2019年9月1日|2019年10月1日 |
| 印度中部 |可用 |完成 |
| 印度南部 |可用 |完成 |
| 印度西部 |2019年7月1日 |進行中 |
| 日本東部 |可用 |完成 |
| 日本西部 |可用 |完成 |
| 南韓中部 |可用 |完成 |
| 南韓南部 |可用 |完成 |
| 美國中北部 |可用 |完成 |
| 北歐 |可用 |完成 |
| 南非北部 |2019年7月12日 |完成 |
| 美國中南部 |可用 |完成 |
| 東南亞 |可用 |完成 |
| 阿拉伯聯合大公國北部 |2019年7月20日 |完成 |
| 英國南部 |可用 |進行中 |
| 英國西部 |可用 |進行中 |
| 美國中西部 |2019年9月1日 |2019年10月1日|
| 西歐 |可用 |完成 |
| 美國西部 |可用 |完成 |
| 美國西部 2 |可用 |完成 |

## <a name="automatic-upgrade-process"></a>自動升級程序

根據上述的可用性圖表, 我們將為您的 Gen1 實例排程自動升級。 為了避免資料倉儲可用性發生任何未預期的中斷，自動化升級將會排在維護排程期間。 在自動升級至 Gen2 的區域中, 將會停用建立新 Gen1 實例的功能。 完成自動升級之後, Gen1 將會被取代。 如需排程的詳細資訊，請參閱[檢視維護排程](viewing-maintenance-schedule.md)

當我們重新開機您的資料倉儲時, 升級程式會包含連線的短暫下降 (大約5分鐘)。  重新啟動後，您的資料倉儲就會完全可供使用。 不過, 當升級程式繼續在背景中升級資料檔案時, 您可能會遇到效能降低的情況。 效能降低的總時間會因您的資料檔案大小而有所不同。

在重新啟動之後，您也可以使用較大的 SLO 和資源類別，在所有主要資料行存放區資料表上執行 [Alter Index rebuild](sql-data-warehouse-tables-index.md)，藉此加速資料檔案升級程序。

> [!NOTE]
> Alter Index rebuild 是一種離線作業，資料表在重建完成後才可使用。

## <a name="self-upgrade-to-gen2"></a>自我升級至 Gen2

您可以在現有的 Gen1 資料倉儲上遵循下列步驟, 選擇自行升級。 如果您選擇自行升級, 您必須在您的區域開始自動升級程式之前, 先完成此操作。 這麼做可確保您避免因自動升級而造成衝突的任何風險。

進行自我升級時，有兩個選項。  您可以就地升級目前的資料倉儲，也可以將 Gen1 資料倉儲還原至 Gen2 執行個體。

- [就地升級](upgrade-to-latest-generation.md) - 這個選項會將您現有的 Gen1 資料倉儲升級至 Gen2。 當我們重新開機您的資料倉儲時, 升級程式會包含連線的短暫下降 (大約5分鐘)。  重新啟動後，您的資料倉儲就會完全可供使用。 如果您在升級期間遇到問題, 請開啟[支援要求](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket), 並參考「Gen2 升級」做為可能的原因。
- [從還原點升級](sql-data-warehouse-restore.md) - 在目前的 Gen1 資料倉儲上建立使用者定義的還原點，然後直接還原到 Gen2 執行個體。 現有的 Gen1 資料倉儲會保持原狀。 完成還原後，您的 Gen2 資料倉儲就會完全可供使用。  在已還原的 Gen2 執行個體上執行所有測試和驗證程序之後，才能刪除原始的 Gen1 執行個體。

   - 步驟 1:從 Azure 入口網站，[建立使用者定義的還原點](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal)。
   - 步驟 2:從使用者定義的還原點還原時，將「效能層級」設定為您慣用的 Gen2 層。

當升級程序繼續在背景中升級資料檔案時，您可能會遇到效能降低的期間。 效能降低的總時間會因您的資料檔案大小而有所不同。

為了加速背景資料移轉程序，您可以用較大的 SLO 和資源類別，在您會查詢的所有主要資料行存放區資料表上執行 [Alter Index rebuild](sql-data-warehouse-tables-index.md)，立即強制執行資料移動。

> [!NOTE]
> Alter Index rebuild 是一種離線作業，資料表在重建完成後才可使用。

如果您遇到任何關於資料倉儲的問題，請建立[支援票證](sql-data-warehouse-get-started-create-support-ticket.md)以及將「Gen2 升級」當作可能原因。

如需詳細資訊，請參閱[升級至 Gen2](upgrade-to-latest-generation.md)。

## <a name="migration-frequently-asked-questions"></a>移轉常見問題集

**問：Gen2 與 Gen1 的成本相同嗎？**

- 答：是的。

**問：升級會如何影響我的自動化指令碼？**

- 答：任何參考服務等級目標的自動化指令碼都應該變更為對應至 Gen2 對等項目。  請參閱[這裡](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)的詳細資料。

**問：自我升級通常需要花費多久時間？**

- 答：您可以就地升級或從還原點升級。  
   - 就地升級會導致資料倉儲短暫地暫停和繼續。  資料倉儲上線時，就會繼續進行背景程序。  
   - 如果您要透過還原點升級，則需花費較長的時間，因為升級會經歷完整的還原程序。

**問：自動升級需要多久時間？**

- 答：升級的實際停機時間就只是暫停並繼續服務所需的時間，這介於 5 到 10 分鐘。 在短暫的停機之後，背景程序會執行儲存體移轉。 背景程序的時間長度取決於您的資料倉儲大小。

**問：此自動升級何時會進行？**

- 答：在您的維護排程期間。 運用您選擇的維護排程會盡可能不中斷您的業務。

**問：如果背景升級程序似乎停滯，我該怎麼辦？**

 - 答：開始重新編製資料行存放區資料表的索引。 請注意，在此作業期間，重新編製資料表的索引將會離線進行。

**問：如果 Gen2 沒有我在 Gen1 上擁有的服務等級目標，該怎麼辦？**
- 答：如果您在 Gen1 上執行 DW600 或 DW1200，建議分別使用 DW500c 或 DW1000c，因為 Gen2 提供比 Gen1 更多的記憶體、資源和更高的效能。

**問：可以停用異地備份嗎？**
- 答：資料分割 異地備份是企業版功能，可在區域變得無法使用時，保留您的資料倉儲可用性。 如果您有進一步考量，請開啟[支援要求](sql-data-warehouse-get-started-create-support-ticket.md)。

**問：Gen1 與 Gen2 之間的 T-SQL 語法有差異嗎？**

- 答：Gen1 與 Gen2 之間的 T-SQL 語言語法沒有任何變更。

**問：Gen2 是否支援維護時段？**

- 答：是的。

**問：在我的區域升級之後，我可以建立新的 Gen1 執行個體嗎？**

- 答：資料分割 在區域升級之後，新 Gen1 執行個體的建立將會停用。

## <a name="next-steps"></a>後續步驟

- [升級步驟](upgrade-to-latest-generation.md)
- [維護時段](maintenance-scheduling.md)
- [資源健康情況監視器](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [在開始移轉之前檢閱](upgrade-to-latest-generation.md#before-you-begin)
- [就地升級和從還原點升級](upgrade-to-latest-generation.md)
- [建立使用者定義的還原點](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [了解如何還原至 Gen2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [開始 SQL 資料倉儲支援要求](https://go.microsoft.com/fwlink/?linkid=857950)
