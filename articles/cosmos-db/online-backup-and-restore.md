---
title: 在 Azure Cosmos DB 中進行自動的線上備份及隨選資料還原
description: 本文說明自動線上備份及隨選資料還原在 Azure Cosmos DB 中的運作方式。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 066549f1343eaceb9a47fccc3b5d4508f226a89b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967469"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中進行線上備份及隨選資料還原

Azure Cosmos DB 會自動地定期備份您的資料。 自動備份的進行不會影響資料庫作業的效能或可用性。 所有備份會儲存在另外的儲存體服務中，而且系統會全域複寫這些備份，以便為區域性災害提供復原功能。 當您不小心刪除或更新 Azure Cosmos 帳戶、資料庫或容器，並在之後需要復原資料時，自動備份會很有幫助。

## <a name="automatic-and-online-backups"></a>自動的線上備份

有了 Azure Cosmos DB，不只您的資料，還有資料的備份都一併具有高度備援性，可針對區域性災害進行復原。 下列步驟會示範 Azure Cosmos DB 執行資料備份的方式：

* Azure Cosmos DB 會自動帶您資料庫的備份每隔 4 小時，並在任何時間點，只有最新的 2 個備份會儲存。 不過，如果容器或資料庫已刪除，Azure Cosmos DB 只會將指定容器或資料庫的現有快照集保留 30 天。

* Azure Cosmos DB 會將這些備份儲存在 Azure Blob 儲存體中，而實際的資料位於本機 Azure Cosmos DB 內。

*  若要保證低延遲，您的備份快照會儲存在目前的寫入區域相同區域中的 Azure Blob 儲存體 (或其中一個寫入區域中，如果您有多重主機組態) 的 Azure Cosmos 資料庫帳戶。 為了從區域性災害中復原，系統會再透過異地備援儲存體 (GRS)，將 Azure Blob 儲存體中的每個備份資料快照集複寫到另一個區域。 至於會將備份複寫到哪個區域，則取決於來源區域以及與來源區域相關聯的區域配對。 若要深入了解，請參閱 [Azure 區域的異地備援配對清單](../best-practices-availability-paired-regions.md)一文。 您無法直接存取此備份。 只有在起始了備份還原時，Azure Cosmos DB 才會使用此備份。

* 備份的進行不會影響應用程式的效能或可用性。 Azure Cosmos DB 會在背景中執行資料備份，既不會取用任何另外佈建的輸送量 (RU)，也不會影響資料庫的效能和可用性。

* 如果您不小心刪除或損毀您的資料，您應該連絡[Azure 支援](https://azure.microsoft.com/support/options/)8 小時內，讓 Azure Cosmos DB 小組可以協助您從備份還原資料。

下圖顯示三個主要實體分割區都在美國西部的 Azure Cosmos 容器，會備份到美國西部的遠端 Azure Blob 儲存體帳戶，然後再複寫到美國東部：

![GRS Azure 儲存體中所有 Cosmos DB 實體的定期完整備份](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>可用來管理自有備份的選項

使用 Azure Cosmos DB SQL API 帳戶時，您也可以藉由下列其中一個方法，來維護您自己的備份：

* 使用 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 將資料定期移動到所選儲存體。

* 使用 Azure Cosmos DB [變更摘要](change-feed.md)，來定期讀取完整備份以及增量變更的資料，並將資料儲存到您自己的儲存體。

## <a name="backup-retention-period"></a>備份保留期限

Azure Cosmos DB 會每四個小時擷取一次資料的快照集。 任何時候都只會保留最後兩個快照集。 不過，如果容器或資料庫已刪除，Azure Cosmos DB 只會將指定容器或資料庫的現有快照集保留 30 天。

## <a name="restoring-data-from-online-backups"></a>從線上備份還原資料

下列情況可能會意外刪除或修改資料：  

* 整個 Azure Cosmos 帳戶遭到刪除

* 一或多個 Azure Cosmos 資料庫遭到刪除

* 一或多個 Azure Cosmos 容器遭到刪除

* 容器內的 Azure Cosmos 項目 (例如，文件) 遭到刪除或修改。 這種特定情況一般稱為「資料損毀」。

* 共用供應項目資料庫或其內的容器遭到刪除或損毀

Azure Cosmos DB 可以在遇到上述所有情況時還原資料。 還原程序一律會建立新的 Azure Cosmos 帳戶，以容納還原的資料。 新帳戶的名稱 (若未指定) 會有此格式：`<Azure_Cosmos_account_original_name>-restored1`。 如果嘗試了多次還原，末尾的數字會遞增。 您無法將資料還原至預先建立好的 Azure Cosmos 帳戶。

當 Azure Cosmos 帳戶遭到刪除時，我們可以將資料還原至有相同名稱的帳戶 (前提是該帳戶名稱無人使用)。 在這類情況下，建議您不要重新建立已刪除的帳戶，因為這麼做不只會讓還原的資料無法使用相同名稱，還會讓您更加難以知道該從哪個正確帳戶還原。 

Azure Cosmos 資料庫遭到刪除時，您可以還原整個資料庫，也可以只還原該資料庫中的部分容器。 此外，還能跨資料庫選取容器來加以還原，而且所還原的資料全都會放在新的 Azure Cosmos 帳戶中。

當容器內的一或多個項目不小心遭到刪除或變更 (即資料損毀案例)，您必須指定要還原到哪個時間點。 時間是此案例的關鍵。 因為容器處於運作狀態，所以備份仍在執行，如果您等候超過保留期間 (預設值為八小時) 才還原，系統將會覆寫備份。 在遭到刪除的案例中，因為備份週期不會覆寫資料，所以資料不會再儲存起來。 針對已遭到刪除的資料庫或容器，其備份會儲存 30 天。

如果您在資料庫層級佈建輸送量 (也就是一組容器會共用所佈建的輸送量)，則在此案例中，系統會在整個資料庫層級 (而非個別的容器層級) 進行備份和還原程序。 在這類案例中，您無法選取一部分的容器來還原。

## <a name="migrating-data-to-the-original-account"></a>將資料遷移至原始帳戶

資料還原的主要目標是提供方法供您復原不小心刪除或修改的任何資料。 因此，建議您先檢查所復原資料的內容，以確保所含資料符合您的預期。 然後，著手將資料遷移回到主要帳戶。 雖然您可以將還原的帳戶作為運作中的帳戶，但如果您有生產工作負載，則不建議這麼做。  

下列不同方法可供您將資料遷移回到原始的 Azure Cosmos 帳戶：

* 使用 [Cosmos DB 資料移轉工具](import-data.md)
* 使用 [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* 在 Azure Cosmos DB 中使用[變更摘要](change-feed.md) 
* 撰寫自訂程式碼

請在遷移完成後立即刪除所還原的帳戶，否則會持續產生費用。

## <a name="next-steps"></a>後續步驟

接下來，您可以了解如何從 Azure Cosmos 帳戶還原資料，或了解如何將資料遷移至 Azure Cosmos 帳戶

* 若要提出還原要求，請[從 Azure 入口網站提出票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以連絡 Azure 支援
* [如何從 Azure Cosmos 帳戶還原資料](how-to-backup-and-restore.md)
* [使用 Cosmos DB 變更摘要](change-feed.md)將資料移至 Azure Cosmos DB。
* [使用 Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 將資料移至 Azure Cosmos DB。

