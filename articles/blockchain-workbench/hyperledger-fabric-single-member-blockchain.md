---
title: Hyperledger Fabric 聯盟
description: 使用 Hyperledger Fabric 聯盟解決方案範本來部署和設定單一成員網路
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 28561f5f94044d19cfd07e99d7f7a736ec470cf1
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960350"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger Fabric 單一成員網路

您可以使用 Hyperledger Fabric 聯盟解決方案範本來部署和設定 Hyperledger Fabric 單一成員 (多節點) 網路。

閱讀本文之後，您將能夠：

- 獲得區塊鏈、Hyperledger Fabric 以及更複雜聯盟網路架構的運用知識
- 了解如何從 Azure 入口網站內部署和設定單一成員 Hyperledger Fabric 聯盟網路

## <a name="about-blockchain"></a>關於區塊鏈

如果您不熟悉區塊鏈社群，但想要在 Azure 上以簡單且易於設定的方式了解此技術，此時是很好的機會。 區塊鏈是比特幣背後的基礎技術，但區塊鏈不只是促成虛擬貨幣的功臣而已。 區塊鏈是現有資料庫、分散式系統和密碼編譯技術的複合體，不僅能夠安全地進行多方運算，還能保證不變性、驗證性、稽核性，以及從攻擊中復原的能力。 不同的通訊協定會利用不同的機制來提供這些屬性。 [Hyperledger Fabric](https://github.com/hyperledger/fabric) 就是一個這樣的通訊協定。

## <a name="consortium-architecture-on-azure"></a>Azure 上的聯盟架構

此範本會部署拓撲，以協助單一組織內的使用者 (單一成員) 測試和模擬生產環境。 此部署由單一區域的多節點網路組成，但很快便會擴大為多個區域。

此網路由三種節點組成：

1. **成員節點**：此節點會執行 Fabric 成員資格服務，以註冊和管理網路成員。 此節點最終可形成叢集，以實現延展性和高可用性，不過，本實驗室會使用單一成員節點。
2. **排序者節點**：此節點會執行通訊服務以實作傳遞保證，例如，全序廣播或不可部分完成的交易。
3. **對等節點**：此節點會認可交易，並維護狀態以及分散式總帳複本。

## <a name="getting-started"></a>開始使用

若要開始，您需要可支援部署數個虛擬機器和標準儲存體帳戶的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可[建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

根據預設，大部分的訂用帳戶類型都可支援小型部署拓撲，而不必增加配額。 一個成員的最小可能部署會需要：

- 5 個虛擬機器 (5 個核心)
- 1 個 VNet
- 1 個負載平衡器
- 1 個公用 IP 位址

擁有訂用帳戶後，請移至 [Azure 入口網站](https://portal.azure.com)。 選取 [+]，選取 [區塊鏈]，然後選取 [Hyperledger Fabric 單一成員區塊鏈]。

![Hyperledger Fabric 單一成員區塊鏈 Marketplace 範本](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>部署

若要開始，請選取 [Hyperledger Fabric 單一成員區塊鏈]，然後按一下 [建立]。 這會開啟精靈的 [基本資料] 刀鋒視窗。

[範本部署] 會引導您設定多節點網路。 部署流程分成三個步驟：基本資料、網路設定和 Fabric 設定。

### <a name="basics"></a>基本概念

在 [基本資料] 刀鋒視窗下，指定任何部署的標準參數值，例如訂用帳戶、資源群組和基本虛擬機器屬性。

![基本概念](./media/hyperledger-fabric-single-member-blockchain/basics.png)

參數名稱| 說明| 允許的值|預設值
---|---|---|---
**資源前置詞**| 用來作為所部署資源命名基底的字串。|6 個字元以下|NA
**VM 使用者名稱**| 為此成員所部署的每個虛擬機器，其管理員的使用者密碼。|1 - 64 個字元|azureuser
**驗證類型**| 用來向虛擬機器驗證的方法。|密碼或 SSH 公開金鑰|密碼
**密碼 (驗證類型 = 密碼)**|每個所部署虛擬機器的管理員帳戶密碼。 密碼必須包含下列其中 3 項要求：1 個大寫字元、1 個小寫字元、1 個數字與 1 個特殊字元。<br /><br />所有 VM 一開始都有相同的密碼，但您可以在佈建之後變更密碼。|12 - 72 個字元|NA
**SSH 金鑰 (驗證類型 = 公開金鑰)**|用於遠端登入的安全殼層金鑰。||NA
**依 IP 位址限制存取**|此設定可判斷是否要對該類型限制用戶端端點存取。|是/否| 否
**允許的 IP 位址或子網路 (依 IP 位址 = Yes 來限制存取)**|當存取控制啟用時，允許存取用戶端端點的單一 IP 位址或一組 IP 位址。||NA
**訂用帳戶** |要作為部署目的地的訂用帳戶。
**資源群組** |要對其部署聯盟網路的資源群組。||NA
**位置** |要作為第一個成員網路使用量部署目的地的 Azure 區域。

### <a name="network-size-and-performance"></a>網路大小和效能

接下來，在 [網路大小和效能] 下指定聯盟網路大小的輸入，例如，成員資格節點、排序者節點和對等節點的數目。 選擇基礎結構選項和虛擬機器大小。

![網路大小和效能](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

參數名稱| 說明| 允許的值|預設值
---|---|---|---
**成員資格節點數目**|執行成員資格服務的節點數目。 如需成員資格服務的其他詳細資料，請查看 Hyperledger [文件](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf)下的＜安全性和成員資格服務＞。<br /><br />此值目前限制為 1 個節點，但我們計劃在下一個修訂版本支援透過叢集進行相應放大。|1| 1
**排序者節點數目** |將交易排序 (組織) 到區塊的節點數目。--> 此陳述過於冗長且容易混淆。 如需排序服務的其他詳細資料，請瀏覽 Hyperledger [文件](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html)。<br /><br />此值目前限制為 1 個節點。 |1 |1
**對等節點的數目**| 執行交易並維持狀態和總帳複本的聯盟成員所擁有的節點。<br /><br />如需排序服務的其他詳細資料，請瀏覽 Hyperledger [文件](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)。|3| 3 - 9
**儲存體效能**|支援每個所部署節點的儲存體類型。 若要深入了解儲存體，請瀏覽 [Microsoft Azure 儲存體簡介](https://docs.microsoft.com/azure/storage/common/storage-introduction)和[進階儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)。|標準或進階|標準
**虛擬機器大小** |網路中所有節點所使用的虛擬機器大小|標準 A、<br />標準 D、<br />標準 D-v2、<br />標準 F 系列、<br />標準 DS <br />及標準 FS|標準 D1_v2

### <a name="fabric-specific-settings"></a>Fabric 專屬設定

最後，在 [Fabric 設定] 下，指定 Fabric 相關組態設定。

![Fabric 設定](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

參數名稱| 說明| 允許的值|預設值
---|---|---|---
**啟動程序使用者名稱**| 這是初始的授權使用者，會向所部署網路中的成員服務註冊。|9 個以下的字元|admin
**Fabric CA 的啟動程序使用者密碼**|管理員密碼，用來保護匯入成員資格節點的 Fabric CA 帳戶。<br /><br />密碼必須包含 1 個大寫字元、1 個小寫字元及 1 個數字。|12 個以上的字元|NA

### <a name="deploy"></a>部署

在 [摘要] 中，檢閱所指定的輸入，並執行基本的預先部署驗證。

![總結](./media/hyperledger-fabric-single-member-blockchain/summary.png)

檢閱法律和隱私權條款，然後按一下 [購買] 以進行部署。 根據所要佈建的 VM 數目，部署時間可能會從幾分鐘到數十分鐘不等。

### <a name="accessing-nodes"></a>存取節點

部署完成時，便會顯示 [概觀]。

![部署](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

如果此畫面未自動顯示 (或許是因為您在部署執行時於管理入口網站周圍移動)，您隨時可在左側導覽列的 [資源群組] 索引標籤中找到它。 在您於步驟 1 所輸入的資源群組名稱上按一下，以移至 [概觀] 頁面。

[概觀] 會列出解決方案範本所部署的所有資源。 您可以隨意瀏覽這些資源，但您也可以從這個畫面存取範本所產生的_輸出參數_。 這些輸出參數會在連線至 Hyperledger Fabric 網路時提供實用的資訊。

若要存取輸出參數，請先按一下 [資源群組] 刀鋒視窗中的 [部署] 索引標籤。 隨即會顯示部署歷程記錄。

![部署歷程記錄](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

從部署歷程記錄中，按一下清單中的第一個部署以查看詳細資料。

![部署詳細資料](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

詳細資料畫面會顯示部署摘要，且後面會有三個實用的輸出參數：

- API-ENDPOINT，可供在網路上部署應用程式後使用。
- PREFIX，也稱為「部署前置詞」，可唯一識別資源與部署。 在使用命令列型工具時會用到此參數。
- SSH-TO-FIRST-VM，可提供您預先組合的 ssh 命令與所需的所有正確參數，以便連線至網路中的第一個 VM；在 Hyperledger Fabric 案例中，這會是 Fabric-CA 節點。

您可以透過 SSH 及所提供的管理使用者名稱和密碼/SSH 金鑰，從遠端連線至每個節點的虛擬機器。 由於節點 VM 不會有自己的公用 IP 位址，因此您必須通過負載平衡器並指定連接埠號碼。 用來存取第一個交易節點的 SSH 命令是第三個範本輸出 **SSH-TO-FIRST-VM (如需部署範例：`sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`)。 若要前往其他交易節點，請將連接埠號碼累加 1 (例如，第一個交易節點在連接埠 3000 上、第二個在 3001 上、第三個在 3002 上，依此類推)。

## <a name="next-steps"></a>後續步驟

您現在可以將焦點放在如何透過 Hyperledger 聯盟區塊鏈網路，來進行應用程式和鏈碼開發。
