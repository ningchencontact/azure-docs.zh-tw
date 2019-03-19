---
title: Azure 上的 Hyperledger Fabric 聯盟網路
description: 部署和設定Hyperledger Fabric 聯盟網路的解決方案範本
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: ce1afbd5499e798888e77f52d7b652e7e1f548fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005824"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger Fabric 聯盟網路

您可以使用 Hyperledger Fabric 聯盟解決方案範本在 Azure 上部署和設定 Hyperledger Fabric 聯盟網路。

閱讀本文之後，您將能夠：

- 獲得區塊鏈、Hyperledger Fabric 以及更複雜聯盟網路架構的運用知識
- 了解如何從 Azure 入口網站內部署和設定 Hyperledger Fabric 聯盟網路

## <a name="about-blockchain"></a>關於區塊鏈

如果您不熟悉區塊鏈社群，但想要在 Azure 上以簡單且易於設定的方式了解此技術，此解決方案範本是很好的機會。 區塊鏈是比特幣背後的基礎技術，但區塊鏈不只是促成虛擬貨幣的功臣而已。 區塊鏈是現有資料庫、分散式系統和密碼編譯技術的複合體，不僅能夠安全地進行多方運算，還能保證不變性、驗證性、稽核性，以及從攻擊中復原的能力。 不同的通訊協定會利用不同的機制來提供這些屬性。 [Hyperledger Fabric](https://github.com/hyperledger/fabric) 就是一個這樣的通訊協定。

## <a name="consortium-architecture-on-azure"></a>Azure 上的聯盟架構

若要在 Azure 中啟用 Hyperledger Fabric，我們支援兩種主要的部署類型。 這些部署是依據所需目標，專為容納不同拓撲所設計的。

- **單一虛擬機器、開發人員伺服器** - 此部署類型設計成開發環境，用來在 Hyperledger Fabric 上建置及測試解決方案組建。
- **多部虛擬機器、相應放大部署** - 此部署類型是針對利用共用環境為不同參與者建立聯盟模型的環境所設計。

在這兩種部署中，構成 Hyperledger Fabric 的建置區塊都是相同的。  部署中的差異在於這些元件的相應放大方式。

- **CA 節點**：執行憑證授權單位的節點，系統會使用憑證授權單位產生在網路中用於識別的憑證。
- **排序者節點**：此節點會執行通訊服務以實作傳遞保證，例如，全序廣播或不可部分完成的交易。
- **對等節點**：此節點會認可交易，並維護狀態以及分散式總帳複本。
- **CouchDB 節點**：可執行 CouchDB 服務的節點。CouchDB 服務可以保留狀態資料庫，並提供豐富的鏈碼 (chaincode) 資料查詢，可從簡單的索引鍵/值擴充至 JSON 類型儲存體。

### <a name="single-virtual-machine-architecture"></a>單一虛擬機器架構

如先前所述，單一虛擬機器架構是針對開發人員建置，配備一部用於開發應用程式的低使用量伺服器。 顯示的所有容器都是在單一虛擬機器中執行的。 此組態會使用 [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) 排序服務。 此組態*不是*錯誤容錯排序服務，但是採用適用於針對開發用途的清量型設計。

![單一虛擬機器架構](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>多個虛擬機器架構

多個虛擬機器、相應放大架構建置具備高可用性，且調整每個核心元件。 此架構更適用於生產環境等級部署。

![多個虛擬機器架構](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>開始使用

若要開始，您需要可支援部署數個虛擬機器和標準儲存體帳戶的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可[建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

擁有訂用帳戶後，請移至 [Azure 入口網站](https://portal.azure.com)。 選取 [建立資源] > [區塊鏈] > [Hyperledger Fabric 聯盟]。

![Hyperledger Fabric 單一成員區塊鏈 Marketplace 範本](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>部署

在 [Hyperledger Fabric 聯盟] 範本中，選取 [建立]。

範本部署會引導您設定多節點 [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) 網路。 部署流程分成下列四個步驟：基本資料、聯盟網路設定、網狀架構設定和選擇性元件。

### <a name="basics"></a>基本概念

在 [基本資料] 中，為任何部署指定標準參數的值。 例如，訂用帳戶、資源群組及基本虛擬機器屬性。

![基本概念](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| 參數名稱 | 描述 | 允許的值 |
|---|---|---|
**資源前置詞** | 部署時所佈建資源的名稱前置詞 |6 個字元以下 |
**使用者名稱** | 為此成員所部署的每個虛擬機器，其管理員的使用者名稱 |1 - 64 個字元 |
**驗證類型** | 用來向虛擬機器驗證的方法 |密碼或 SSH 公開金鑰|
**密碼 (驗證類型 = 密碼)** |每個所部署虛擬機器的管理員帳戶密碼。 密碼必須包含下列字元類別的其中三種：1 個大寫字元、1 個小寫字元、1 個數字與 1 個特殊字元<br /><br />所有 VM 一開始都有相同的密碼，但您可以在佈建之後變更密碼|12 - 72 個字元|
**SSH 金鑰 (驗證類型 = SSH 公開金鑰)** |用於遠端登入的安全殼層金鑰 ||
**訂用帳戶** |要作為部署目的地的訂用帳戶 ||
**資源群組** |要對其部署聯盟網路的資源群組 ||
**位置** |要在其中部署第一個成員的 Azure 區域 ||

選取 [確定] 。

### <a name="consortium-network-settings"></a>聯盟網路設定

在 [網路設定] 中，指定輸入來建立或加入現有的聯盟網路，並設定您的組織設定。

![聯盟網路設定](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| 參數名稱 | 描述 | 允許的值 |
|---|---|---|
**網路組態** |您可以選擇建立新的，或加入現有的網路。 如果選擇*加入現有*，您需要提供其他的值。 |新增網路 <br/> 加入現有的 |
**HLF CA 密碼** |用於部署時建立的憑證授權單位所產生憑證的密碼。 密碼必須包含下列字元類別的其中三種：1 個大寫字元、1 個小寫字元、1 個數字與 1 個特殊字元。<br /><br />所有虛擬機器一開始都有相同的密碼，但您可以在佈建之後變更密碼。|1 - 25 個字元 |
**組織設定** |您可以自訂組織的名稱和憑證，或使用預設值。|預設值 <br/> 進階 |
**VPN 網路設定** | 佈建 VPN 通道閘道以存取 VM | 是 <br/> 否 |

選取 [確定] 。

### <a name="fabric-specific-settings"></a>網狀架構特有設定

在 [網狀架構組態] 中，您可以設定網路的大小和效能，並指定網路可用性的輸入。 例如，數字排序者和對等節點、每個節點使用的持續性引擎，以及 VM 大小。

![Fabric 設定](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| 參數名稱 | 描述 | 允許的值 |
|---|---|---|
**調整類型** |部署類型可以是單一虛擬機器具備多個容器，或在相應放大模型中具備多個虛擬機器。|單一 VM 或多個 VM |
**VM 磁碟類型** |支援每個所部署節點的儲存體類型。 <br/> 若要深入了解可用的磁碟類型，請參閱[選取磁碟類型](../../virtual-machines/windows/disks-types.md)。|標準 SSD <br/> 進階 SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>多個 VM 部署 (額外設定)

![適用於多個 VM 部署的網狀架構設定](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| 參數名稱 | 描述 | 允許的值 |
|---|---|---|
**排序者節點數目** |將交易排序 (組織) 到區塊的節點數目。 <br />如需排序服務的其他詳細資料，請瀏覽 Hyperledger [文件](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) |1 - 4 |
**排序者節點的虛擬機器大小** |網路中排序者節點所使用的虛擬機器大小|標準 Bs、<br />標準 Ds、<br />標準 FS |
**對等節點的數目** | 執行交易並維持狀態和總帳複本的聯盟成員所擁有的節點。<br />如需排序服務的其他詳細資料，請瀏覽 Hyperledger [文件](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)。|1 - 4 |
**節點強制持續性** |對等節點所使用的持續性引擎。 您可以為每個對等節點設定此引擎。 查看下方多個對等節點的詳細資料。|CouchDB <br />LevelDB |
**對等節點的虛擬機器大小** |網路中所有節點所使用的虛擬機器大小|標準 Bs、<br />標準 Ds、<br />標準 FS |

### <a name="multiple-peer-node-configuration"></a>多個對等節點設定

此範本可讓您挑選每個對等節點的持續性引擎。 例如，如果您有三個對等節點，就可以在其中之一使用 CouchDB，在另外兩個使用 LevelDB。

![多個對等節點設定](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

選取 [確定] 。

### <a name="deploy"></a>部署

在 [摘要] 中，檢閱所指定的輸入，並執行基本的預先部署驗證。

![總結](./media/hyperledger-fabric-consortium-blockchain/summary.png)

檢閱法律和隱私權條款，然後選取 [購買] 以進行部署。 根據所要佈建的 VM 數目，部署時間可能會從幾分鐘到數十分鐘不等。

## <a name="next-steps"></a>後續步驟

您現在可以將焦點放在如何透過 Hyperledger 聯盟區塊鏈網路，來進行應用程式和鏈碼開發。
