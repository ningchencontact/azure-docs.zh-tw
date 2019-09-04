---
title: Azure 區塊鏈服務開發總覽
description: 在 Azure 區塊鏈 Service 上開發解決方案的簡介。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 6f27dd199cc054d128a4f46b222c7207d5975efb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241011"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 區塊鏈服務開發總覽

您可以使用 Azure 區塊鏈 Service 建立聯盟區塊鏈網路, 以啟用企業案例, 例如資產追蹤、數位權杖、忠誠度和報酬、供應鏈財務和來源。 本文是 Azure 區塊鏈服務開發的簡介, 以及執行區塊鏈 for enterprise 的重要主題。

## <a name="client-connection-to-azure-blockchain-service"></a>與 Azure 區塊鏈 Service 的用戶端連線

區塊鏈網路有不同類型的用戶端, 包括完整節點、光線節點和遠端用戶端。 Azure 區塊鏈 Service 會建立包含節點的區塊鏈網路。 您可以使用不同的用戶端作為 Azure 區塊鏈 Service 的閘道, 以進行區塊鏈開發。 Azure 區塊鏈 Service 提供基本驗證或存取金鑰作為開發端點。 以下是您可以使用 connect 的熱門用戶端。

### <a name="metamask"></a>MetaMask

MetaMask 是以瀏覽器為基礎的錢包 (遠端用戶端)、RPC 用戶端和基本的合約瀏覽器。 不同于其他瀏覽器錢包, MetaMask 會將 web3 實例插入瀏覽器 JavaScript 內容中, 做為連接到各種乙太坊區塊鏈 (*mainnet*、 *Ropsten testnet*、 *Kovan testnet*、local RPC node、以此類推)。 您可以輕鬆設定自訂 RPC 來連線到 Azure 區塊鏈 Service, 並使用 Remix 開始區塊鏈開發。

### <a name="geth"></a>Geth

Geth 是命令列介面, 可執行在 Go 中執行的完整乙太坊節點。 您不需要執行完整節點, 而可以啟動其互動式主控台, 以提供公開 JavaScript API 的 JavaScript 執行時間環境來與 Azure 區塊鏈 Service 互動。

## <a name="development-framework-configuration"></a>開發架構設定

若要開發精密的企業區塊鏈解決方案, 需要開發架構來連接到不同的區塊鏈網路、管理智慧型合約生命週期、自動化測試、使用腳本部署智慧型合約, 以及提供互動式主控台。

Truffle 是一種熱門的區塊鏈開發架構, 可在乙太坊區塊鏈上撰寫、編譯、部署及測試分散的應用程式。 您也可以將 Truffle 視為一種架構, 它會嘗試順暢地整合智慧型合約開發和傳統 網頁程式開發。

即使是最小的專案也會與至少兩個區塊鏈節點互動:一個在開發人員的電腦上, 另一個則代表開發人員部署其應用程式的網路。 例如, 主要的公用乙太坊網路或 Azure 區塊鏈服務。 Truffle 提供一個系統來管理每個網路的編譯和部署成品, 並以簡化最終應用程式部署的方式來執行此動作。 如需詳細資訊，請參閱[快速入門：使用 Truffle 連接到 Azure 區塊鏈 Service 網路](connect-truffle.md)。

## <a name="ethereum-quorum-private-transaction"></a>乙太坊仲裁私人交易

仲裁是以乙太坊為基礎的分散式總帳通訊協定, 具有交易加上合約隱私權和新的共識機制。 乙太坊的主要增強功能包括:

* 隱私權-仲裁透過公用和私用狀態分隔來支援私用交易和私人合約, 並利用對等加密訊息交換, 將私人資料導向至網路參與者。
* 替代的共識機制-不需要在許可網路中進行工作證明或因應意見證明。 仲裁提供多種針對聯盟鏈 (例如次浮動定位和 IBFT) 所設計的共識機制。  Azure 區塊鏈 Services 會使用 IBFT 共識機制。

* 對等互連-使用智慧合約的節點和對等互連, 確保只有已知的合作物件可以加入網路
* 較高的效能-仲裁提供比公用 Geth 更高的效能

請參閱[教學課程：使用 Azure 區塊鏈服務](send-transaction.md)傳送交易, 以取得私用交易的範例。

## <a name="block-explorers"></a>封鎖流覽

封鎖瀏覽器是可顯示個別區塊內容、交易位址資料和歷程記錄的線上區塊鏈。 基本的區塊資訊可透過 Azure 區塊鏈 Service 中的 Azure 監視器取得, 不過, 如果您在開發期間需要更多詳細資訊, 封鎖流覽流覽會很有用。  您可以使用受歡迎的開放原始碼封鎖流覽者。 以下是可與 Azure 區塊鏈 Service 搭配使用的區塊流覽程式清單:

* 來自 Web3 Labs 的[Azure 區塊鏈 Service Explorer](https://web3labs.com/azure-offer)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS 測量

當區塊鏈在更多企業案例中使用時, 每秒交易數 (TP) 速度非常重要, 以避免發生瓶頸和系統效率不佳的情況。 高交易率可能很容易在非集中式區塊鏈中維護。 精確的 TP 量測可能會受到不同因素的影響, 例如伺服器執行緒、交易佇列大小、網路延遲和安全性。 如果您需要在開發期間測量 TPS 速度, 則會[ChainHammer](https://github.com/drandreaskrueger/chainhammer)熱門的開放原始碼工具。

## <a name="next-steps"></a>後續步驟

[快速入門：使用 Truffle 連接到 Azure 區塊鏈 Service 網路](connect-truffle.md)
