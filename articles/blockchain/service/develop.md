---
title: Azure 區塊鏈 Service 開發概觀
description: 在開發 Azure 區塊鏈服務解決方案的簡介。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027896"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 區塊鏈 Service 開發概觀

使用 Azure 區塊鏈服務時，您可以建立區塊鏈網路，以啟用企業案例，例如資產追蹤、 數位權杖、 忠誠度及 reward、 供應鏈金融和 provenance 協會。 這篇文章是 Azure 區塊鏈服務開發的概觀和實作企業的區塊鏈的重要主題的簡介。

## <a name="client-connection-to-azure-blockchain-service"></a>Azure 區塊鏈服務用戶端連線

有不同類型的區塊鏈網路包括完整的節點、 淺的節點，以及遠端用戶端的用戶端。 Azure 的區塊鏈 Service 會建立包含節點的區塊鏈網路。 區塊鏈開發，您可以為您的閘道，Azure 區塊鏈服務使用不同的用戶端。 Azure 區塊鏈服務會提供基本驗證] 或 [存取金鑰做為開發端點。 以下是您可以使用熱門的用戶端連線。

### <a name="metamask"></a>MetaMask

瀏覽器型 「 電子錢包 」 （遠端用戶端）、 RPC 用戶端，而基本合約總管 MetaMask。 不同於其他的瀏覽器 wallets，MetaMask 插入 web3 執行個體的瀏覽器 JavaScript 內容，做為連接到各種以太坊區塊鏈的 RPC 用戶端 (*mainnet*， *Ropsten testnet*， *Kovan testnet*，RPC 的本機節點，依此類推。)。 您可以設定自訂的 RPC，輕鬆地以連線至 Azure 區塊鏈服務，並開始使用 Remix 的區塊鏈開發。

### <a name="geth"></a>Geth

Geth 是執行完整的 Ethereum 節點中移至實作的命令列介面。 您不需要執行完整的節點，但可以啟動其提供公開 Azure 區塊鏈服務互動的 JavaScript API 的 JavaScript 執行階段環境的互動式主控台。

## <a name="development-framework-configuration"></a>開發架構設定

若要開發複雜的企業區塊鏈解決方案，開發架構需要連線到不同的區塊鏈網路、 管理智慧合約生命週期、 自動化測試、 部署智慧合約指令碼，以及提供互動式主控台。

Truffle 是受歡迎的區塊鏈開發架構來撰寫、 編譯、 部署及測試以太坊區塊鏈上的非集中式應用程式。 您可以也將 Truffle 嘗試完美整合智慧合約開發與傳統 web 開發的架構。

即使最小的專案互動的區塊鏈的至少兩個節點：其中一個為開發人員電腦和其他代表開發人員將其應用程式的部署位置的網路上。 比方說，主要公用以太坊網路或 Azure 區塊鏈的服務。 Truffle 提供的系統管理的編譯和部署的成品，每個網路，並在這麼做可簡化最終的應用程式部署的方式。 如需詳細資訊，請參閱[快速入門：使用 Truffle 連接到 Azure 區塊鏈服務網路](connect-truffle.md)。

## <a name="ethereum-quorum-private-transaction"></a>以太坊仲裁私用的交易

仲裁是使用交易加上合約隱私權和新的共識機制以太坊為基礎的分散式的總帳通訊協定。 透過移至以太坊的索引鍵增強功能包括：

* 隱私權-仲裁支援私用的交易和私用的合約，透過公用和私用狀態區隔，並利用有向私用資料傳送到網路參與者的端對端加密的訊息交換。
* 替代共識機制-不需要授與權限的網路中的工作證明或證明利害關係共識。 仲裁提供多個共識機制，專為協會鏈結，例如浮動定位和 IBFT。  Azure 區塊鏈服務是使用 IBFT 共識機制。

* 對等互連權限-使用智能合約，確保只有已知的合作對象 節點和對等權限可以加入網路
* 較高的效能-仲裁提供更高的效能比公用 Geth

請參閱[教學課程：傳送交易，使用 Azure 區塊鏈 Service](send-transaction.md)做為私用的交易。

## <a name="block-explorers"></a>區塊總管

區塊總管會顯示個別的區塊內容、 交易的地址資料和歷程記錄的線上的區塊鏈瀏覽器。 基本區塊資訊是透過 Azure 區塊鏈 Service 中的 Azure 監視器提供，不過，如果您需要更多的詳細資訊，在開發期間，區塊總管可能會很有用。  有一些您可以使用熱門的開放原始碼區塊總管。 以下是一份 Azure 區塊鏈服務所使用的區塊總管：

* [Azure 區塊鏈服務總管](https://web3labs.com/azure-offer)從 Web3 實驗室
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TP 度量

在多個企業案例中使用區塊鏈時，第二個 (TPS) 速度每筆交易務必避免瓶頸和系統效率不足的情況。 達到高交易率可能難以維護集中式的區塊鏈內。 精確的 TP 度量可能會受到不同因素，例如伺服器執行緒、 交易佇列大小、 網路延遲和安全性。 如果您需要在開發期間測量 TP 速度，受歡迎的開放原始碼工具是[ChainHammer](https://github.com/drandreaskrueger/chainhammer)。

## <a name="next-steps"></a>後續步驟

[快速入門：使用 Truffle 連接到 Azure 區塊鏈服務網路](connect-truffle.md)
