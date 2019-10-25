---
title: 使用 Azure 儲存體建立高度安全、持久、可擴充應用程式的雲端儲存體
description: 瞭解在雲端中儲存大型結構化和非結構化行動應用程式資料的服務。
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795649"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>具有 Azure 儲存體的高安全性、耐用、可擴充應用程式的雲端儲存體
[Azure 儲存體](https://azure.microsoft.com/services/storage/)是適用于新式應用程式的 Microsoft 雲端儲存體解決方案，可為數據物件提供可大幅調整的物件存放區、適用于雲端的檔案系統服務、可靠訊息的訊息存放區，以及 NoSQL 存放區。 Azure 儲存體的特色：
- **持久性和高可用性**-冗余可確保您的資料在發生暫時性硬體失敗時安全。 您也可以選擇在資料中心或地理區域間複寫資料，以便在發生地方性災難或天然災害時獲得額外保護。 以此方式複寫資料，可在發生未預期的中斷事件時保持高可用性。
- **安全**-所有寫入 Azure 儲存體的資料都會由服務加密。 Azure 儲存體在存取您資料的人員控管上，提供更細微的控制。
- **可**調整的服務設計為可大幅調整規模，以符合現今應用程式的資料儲存和效能需求。
- **受控**-Azure 會為您處理硬體維護、更新和重大問題。
- 資料可透過 HTTP 或 HTTPS 從世界各地**存取**。 Microsoft 提供各種不同語言的用戶端程式庫，包括 .NET、JAVA、node.js、Python、PHP、Ruby、Go 等等，以及成熟的 REST API。 Azure PowerShell 或 Azure CLI 支援腳本，而 Azure 入口網站和 Azure 儲存體總管提供了簡單的視覺效果解決方案，可處理您的資料。

使用下列服務在您的行動應用程式中啟用雲端存放裝置。

## <a name="azure-blob-storage"></a>Azure Blob 儲存體
[Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)提供適用于雲端的物件儲存體解決方案，並已針對儲存大量非結構化資料進行優化，而不符合特定資料模型或定義（例如文字或二進位）。 它支援各種語言的用戶端程式庫，其設計目的是：
- 直接提供映像或文件給瀏覽器。
- 儲存檔案供分散式存取。
- 串流傳輸視訊和音訊。
- 寫入記錄檔。
- 儲存備份和還原、災害復原和封存資料。
- 儲存資料供內部部署或 Azure 託管服務進行分析。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Documentation](/azure/storage/blobs/storage-blobs-introduction)
- [快速入門](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [範例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure 表格儲存體
[Azure 表格儲存體](https://azure.microsoft.com/services/storage/tables/)是將結構化的 NoSQL 資料儲存在雲端中的服務，提供具有無架構設計的索引鍵/屬性存放區。 Azure 資料表儲存體可儲存大量的結構化資料。 此服務是 NoSQL 資料存放區，可接受來自 Azure 雲端內部和外部的已驗證呼叫。 Azure 資料表很適合儲存結構化、非關聯式資料。 資料表儲存體的一般用途包括：
- 儲存 Tb 的結構化資料，能夠提供 web 規模的應用程式。
- 儲存不需要複雜聯結、外鍵或預存程式的資料集，而且可以進行反正規化以進行快速存取。
- 使用叢集索引快速地查詢資料。
- 使用 OData 通訊協定和 LINQ 查詢搭配 WCF 資料服務 .NET 程式庫來存取資料。

您可以使用資料表儲存體來儲存和查詢龐大的結構化、非關聯式資料集，且資料表會隨著需求增加而調整。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Documentation](/azure/storage/tables/table-storage-overview)
- [範例](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [快速入門](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure 檔案
[Azure 檔案服務](https://azure.microsoft.com/services/storage/files/)可讓您設定高可用性網路檔案共用，其可使用標準伺服器訊息區塊 (SMB) 通訊協定來存取。 多個 Vm 可以同時與讀取和寫入權限共用相同的檔案。 您也可以使用 REST 介面或儲存體用戶端程式庫來讀取檔案。 您可以使用指向檔案並包含共用存取簽章（SAS）權杖的 URL，從世界各地存取檔案。 您可以產生 SAS 權杖；SAS 權杖可允許特定一段時間內私人資產的特定存取。

Azure 檔案共用可以用來：
- 取代或補充內部部署檔案伺服器：熱門的作業系統（例如 Windows、macOS 和 Linux）可以在世界各地直接掛接 Azure 檔案共用。 透過 Azure 檔案同步，也可以將 Azure 檔案共用複寫到 Windows Server (在內部部署環境或雲端)，從而在資料的使用位置進行高效能和分散式快取。
- 將**應用程式隨即轉移**至雲端，其預期檔案共用會儲存檔案應用程式或使用者資料。
- **簡化雲端開發**： Azure 檔案儲存體也可以用多種方式來簡化新的雲端開發專案。 例如：
    - 共用的應用程式設定：分散式應用程式的常見模式是將設定檔放在可從許多應用程式實例存取的集中位置。 應用程式執行個體可透過 File REST API 載入其組態，而使用者可藉由在本機掛接 SMB 共用來視需要進行存取。
    - 診斷共用： Azure 檔案共用是一個方便的位置，可讓雲端應用程式寫入其記錄、計量和損毀傾印。 應用程式執行個體可以透過 File REST API 寫入記錄，而開發人員可藉由在其本機電腦上掛接檔案共用來存取記錄。 這可提供更多的彈性，因為開發人員可以採用雲端開發，而不必放棄任何其熟悉且喜愛的現有工具。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Documentation](/azure/storage/files/storage-files-introduction)
- [快速入門](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Azure 佇列
[Azure 佇列儲存體](https://azure.microsoft.com/services/storage/queues/)是用來儲存大量訊息的服務。 使用 HTTP 或 HTTPS 透過境過驗證的呼叫，存取來自世界各地的訊息。 佇列訊息的大小上限為 64 KB，而佇列可能包含數百萬則訊息，最高可達儲存體帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Documentation](/azure/storage/queues/)
- [快速入門](/azure/storage/queues/storage-quickstart-queues-portal)
- [範例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
