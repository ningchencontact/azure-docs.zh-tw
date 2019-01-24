---
title: 以太坊工作量證明聯盟解決方案範本
description: 使用「Ethereum 工作量證明聯盟 (Ethereum Proof-of-Work Consortium)」解決方案範本，來部署和設定多成員的聯盟 Ethereum 網路
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 16bf68a5fdb1df2a4f60de9167893a42295cbc52
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260528"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>以太坊工作量證明聯盟解決方案範本

以太坊工作量證明解決方案範本的設計訴求是，讓您以最低限度的 Azure 和以太坊知識，更輕鬆且更快速地部署和設定多成員的聯盟以太坊網路。

透過少數使用者輸入及 Azure 入口網站中的一鍵部署，每個成員都可以使用 Microsoft Azure 計算、網路和儲存體服務，在全球各地佈建他們的網路應用。 每個成員的網路應用都會包含一組負載平衡的交易節點 (應用程式或使用者可透過與其互動來提交交易)、一組用來記錄交易的採礦節點和 VPN 閘道。 後續的連線步驟會與閘道連線，藉以建立已完整設定的多成員區塊鏈網路閘道。

## <a name="about-blockchain"></a>關於區塊鏈

對於剛接觸區塊鏈社群的人們來說，若要在 Azure 上以簡單且易於設定的方式了解此技術，此版解決方案會是個很好的機會。 不過，若要開始，我們建議您先透過此引導式的逐步解說，部署較簡單的獨立以太網路拓撲，然後再建置多成員的聯盟網路。

### <a name="mining-node-details"></a>採礦節點詳細資料

對交易進行採礦的節點會與接受交易的節點分開，以確保這兩個動作不會搶用相同資源。

聯盟成員可以佈建最多五個區域來包含一個或多個採礦節點，並且由受控磁碟支援。 區域中的一個或多個節點會設定為啟動節點，以支援網路中節點的動態搜尋功能。 採礦節點會與其他採礦節點通訊，使基礎分散式總帳的狀態達到一致。 您的應用程式不需要留意這些節點或與之通訊。 由於將焦點放在私人網路，因此採礦節點會獨立於內送的公用網際網路流量，藉此提供第二層保護。 允許輸出流量，但不是流向以太坊探索連接埠。

所有節點都有穩定的 Go Ethereum (Geth) 用戶端版本，並且會設定為採礦節點。 如果您未提供自訂的創世區塊 (genesis block)，則所有節點都會使用相同的以太坊位址，以及由以太坊帳戶密碼保護的金鑰組。 您提供的以太坊複雜密碼會用來產生每個採礦節點的預設帳戶 (coinbase)。 採礦節點會在採礦時收集新增至此帳戶的費用。

每個聯盟成員的採礦節點數量取決於所需的整體網路大小，以及每個成員專有的挖礦算力 (hashing power)。 網路愈大，就需要洩漏愈多節點以取得不公平的優勢。 針對使用虛擬機器擴展集佈建的區域，範本可支援每個區域最多 15 個採礦節點。

### <a name="transaction-node-details"></a>交易節點詳細資料

聯盟成員也會有一組負載平衡交易節點。 這些節點可從虛擬網路外部觸及，因此應用程式可使用這些節點來提交交易或執行區塊鏈內的智慧合約。 所有節點都有穩定的 Go Ethereum (Geth) 用戶端版本，並且已設定為會維護完整的分散式總帳副本。 如果未提供自訂的創世區塊，這些節點就會使用相同的以太坊帳戶，並由以太坊帳戶密碼保護。

交易節點會在可用性設定組內進行負載平衡，以維持高可用性。 針對使用虛擬機器擴展集佈建的區域，範本可支援最多五個採礦節點。

### <a name="log-analytics-details"></a>Log Analytics 詳細資料

每個部署也會建立新的 Log Analytics 執行個體，或可以加入現有的執行個體。 如此可針對組成所部署網路的每個虛擬機器，進行各種效能計量的監視。

## <a name="deployment-architecture"></a>部署架構

### <a name="description"></a>說明

此解決方案範本可以部署單一區域或多區域型的多成員以太坊聯盟網路。 每個區域的虛擬網路都會藉由使用 VNET 閘道和連線資源，來連線至鏈結拓撲中的其他區域。 同時也會佈建登錄器，其中包含每個區域中部署的所有礦工和交易節點必要資訊。

### <a name="standalone-and-consortium-leader-overview"></a>獨立和聯盟領導者概觀

![獨立和聯盟領導者概觀](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>加入聯盟成員概觀

![加入聯盟成員概觀](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>開始使用

此程序需要可支援部署多個虛擬機器擴展集和受控磁碟的 Azure 訂用帳戶。 如有必要，請建立免費的 Azure 帳戶以開始作業。

當訂用帳戶受到保護後，請移至 Azure 入口網站。 選取 [+ 建立資源] 和 [Marketplace (查看全部)]，並搜尋**以太坊工作量證明聯盟**。

範本部署將會引導您設定網路中第一個成員的配置。 部署流程分成下列五個步驟：基本概念、Operations Management Suite、部署區域、網路大小、效能和 Ethereum 設定。

### <a name="basics"></a>基本概念

在 [基本概念] 下，為任何部署指定標準參數值，例如訂用帳戶、資源群組及基本虛擬機器屬性。

![基本概念](./media/ethereum-deployment/sample-deployment.png)

參數名稱|說明| 允許的值|預設值
---|---|---|---
建立新網路或加入現有網路？|建立新網路或加入已存在的聯盟網路|建立新的 加入現有的|建立新的
建立將屬於聯盟的網路？|聯盟網路可允許未來的部署加入此網路 (在上方選取 [建立新的] 時會顯示此項目)|獨立 聯盟|獨立
資源前置詞 |用來作為命名資源基底的字串 (2 到 4 個英數字元)。 將專屬於資源的資訊附加到資源時，部分資源的字串前方會加上唯一的雜湊值。|2 到 4 個英數字元|NA
VM 使用者名稱| 每個所部署 VM 的管理員使用者名稱 (僅限英數字元)|1 - 64 個字元 |gethadmin
驗證類型|用來向虛擬機器驗證的方法。 |密碼或 SSH 公開金鑰|密碼
密碼 (驗證類型 = 密碼)|每個所部署虛擬機器的管理員帳戶密碼。 密碼必須包含下列 3 個需求：1 個大寫字元、1 個小寫字元、1 個數字與 1 個特殊字元。 <br />所有 VM 一開始都有相同的密碼，但您可以在佈建之後變更密碼。|12 - 72 個字元|NA
SSH 金鑰 (驗證類型 = 公開金鑰)|用於遠端登入的安全殼層金鑰。|| NA
訂用帳戶| 要對其部署聯盟網路的訂用帳戶||NA
資源群組| 要對其部署聯盟網路的資源群組。||NA
位置| 資源群組的 Azure 區域。 ||NA



### <a name="operations-management-suite"></a>Operations Management Suite

Operations Management Suite (OMS) 刀鋒視窗可讓您設定網路的 OMS 資源。 OMS 會收集並顯示您網路中實用的計量和記錄，讓您能夠快速檢查網路健康情況或偵錯問題。 一旦容量達到上限，OMS 的免費供應項目將會依正常程序失效。

![建立新 OMS](./media/ethereum-deployment/new-oms.png)

參數名稱|說明| 允許的值|預設值
---|---|---|---
連線至現有的 OMS|建立新的 Log Analytics 執行個體，或加入現有的執行個體|建立新的 加入現有的|建立新的 Log Analytics 位置|將在其中部署新 Log Analytics 的區域 (如果選取 [建立新的] 才會顯示)
現有的 OMS 工作區識別碼|現有執行個體的工作區識別碼 (如果選取 [加入現有的] 才會顯示) OMS 服務層|選擇新執行個體的定價層。 如需更多資訊，請前往 https://azure.microsoft.com/pricing/details/log-analytics/ (如果選取 [加入現有的] 才會顯示)|免費 獨立 每節點|免費
現有 OMS 的主要金鑰|用來連線至現有 OMS 執行個體的主要金鑰 (如果選取 [加入現有的] 才會顯示)

### <a name="deployment-regions"></a>部署區域

接下來，在 [部署區域]下指定 [區域數目] 的輸入，以根據所指定區域數目來部署聯盟網路和 Azure 區域的選取。 使用者可以部署最多五個區域。

![部署區域](./media/ethereum-deployment/deployment-regions.png)

參數名稱| 說明| 允許的值 |預設值
---|---|---|---
區域數目| 要部署聯盟網路的區域數目|1、2、3、4、5| 2
第一個區域| 要部署聯盟網路的第一個區域|所有允許的 Azure 區域| 美國西部
第二個區域 |要部署聯盟網路的第二個區域 (只有在選取 2 作為區域數目時才會顯示)|所有允許的 Azure 區域| 美國東部
第三個區域| 要部署聯盟網路的第三個區域 (只有在選取 3 作為區域數目時才會顯示)|所有允許的 Azure 區域| 美國中部
第四個區域| 要部署聯盟網路的第四個區域 (只有在選取 4 作為區域數目時才會顯示)|所有允許的 Azure 區域| 美國東部 2
第五個區域| 要部署聯盟網路的第五個區域 (只有在選取 5 作為區域數目時才會顯示)|所有允許的 Azure 區域| 美國西部 2

### <a name="network-size-and-performance"></a>網路大小和效能

接下來，在 [網路大小和效能] 底下指定聯盟網路大小的輸入。 例如，採礦節點與交易節點的數量和大小。

![網路大小和效能](./media/ethereum-deployment/network-size-performance.png)

參數名稱 |說明 |允許的值| 預設值
---|---|---|---
採礦節點的數目|每個區域所部署的採礦節點數目|2 - 15| 2
採礦節點儲存體效能|支援每個所部署採礦節點的受控磁碟類型。|標準或進階|標準
採礦節點的虛擬機器大小|採礦節點所使用的虛擬機器大小。|標準 A、 <br />標準 D、 <br />標準 D-v2、 <br />標準 F 系列、 <br />標準 DS  <br />及標準 FS|標準 D1v2
負載平衡交易節點的數目|要佈建為網路一部分的交易節點數目。|1 - 5| 2
交易節點儲存體效能|支援每個所部署交易節點的受控磁碟類型。|標準或進階|標準
交易節點的虛擬機器大小|交易節點所使用的虛擬機器大小。|標準 A、 <br />標準 D、 <br />標準 D-v2、 <br />標準 F 系列、 <br />標準 DS  <br />及標準 FS|標準 D1v2

### <a name="ethereum-settings"></a>以太坊設定

接下來，在 [以太坊設定] 下指定以太坊的相關組態設定，例如網路識別碼和以太坊帳戶密碼或創世區塊。

![以太坊設定](./media/ethereum-deployment/standalone-leader-deployment.png)

參數名稱 |說明 |允許的值|預設值
---|---|---|---
聯盟成員識別碼|與每個聯盟網路成員相關聯的識別碼，用來設定 IP 位址空間，以避免發生衝突。 <br /><br />成員識別碼在相同網路的不同組織間應該是唯一的。 即使相同組織會部署到多個區域，仍然需要唯一成員識別碼。<br /><br />記下此參數值，因為您需要與其他加入的成員共用此參數值。|0 - 255
以太坊網路識別碼|為要部署的聯盟以太坊網路指定網路識別碼。 每個以太坊網路都有自己的網路識別碼，1 是公用網路的識別碼。 雖然僅限採礦節點可存取網路，但我們仍建議使用較大的數字來避免發生衝突。|5 - 999,999,999| 10101010
自訂創世區塊|自動產生創世區塊或提供自訂創世區塊的選項。|是/否| 否
以太坊帳戶密碼 (自訂創世區塊 = 否)|管理員密碼，用來保護匯入每個節點的以太坊帳戶。 密碼必須包含下列：1 個大寫字元、1 個小寫字元和 1 個數字。|12 個以上的字元|NA
以太坊私密金鑰的複雜密碼 (自訂創世區塊 = 否)|用來產生 ECC 私密金鑰的複雜密碼，此金鑰會與所產生的預設以太坊帳戶相關聯。 不需明確傳入預先產生的私密金鑰。<br /><br />複雜密碼應具有足夠的隨機性，以確保私密金鑰夠安全，且不會與其他聯盟成員重疊。 複雜密碼至少必須包含下列：1 個大寫字元、1 個小寫字元和 1 個數字。<br /><br />請注意，如果兩個成員都使用相同的複雜密碼，則會產生相同的帳戶。 如果單一組織想嘗試進行跨區域部署，而且在所有節點間共用單一帳戶 (coin base)，則適用相同的複雜密碼。|12 個以上的字元|NA
創世區塊 (自訂創世區塊 = 是)|代表自訂創世區塊的 JSON 字串。 您可以在此找到有關創世區塊格式的詳細資料 (位在 [自訂網路] 下方)。<br /><br />若提供自訂創世區塊，仍會建立以太坊帳戶。 請考慮在創世區塊中指定已預先準備資金的以太坊帳戶，如此可不用等待採礦。|有效的 JSON |NA
用於連線的共用金鑰|在 VNET 閘道之間進行連線所需的共用金鑰。| 12 個以上的字元|NA
聯盟資料 URL|指向相關聯盟組態資料的 URL，而此組態資料是由另一個成員的部署所提供。 <br /><br />此資訊是由已連線且已有部署的成員所提供。 如果您已部署網路的其餘部分，則 URL 會是範本部署的輸出，名為 CONSORTIUM-DATA。||NA
要與之連線的 VNet 閘道|要與之連線的 VNet 閘道資源路徑。<br />此資訊是由已連線且已有部署的成員所提供。 如果您已部署網路的其餘部分，則 URL 會在範本部署的輸出中，名為 CONSORTIUM_MEMBER_GATEWAY_ID。 注意：必須使用相同成員的聯盟資料 URL 和 VNet 閘道資源。||NA
同儕節點資訊登錄器的端點|同儕節點資訊端點由另一個成員的部署所提供|聯盟中第一個成員的有效端點|NA
同儕節點資訊登錄器的金鑰|同儕節點資訊主要金鑰由另一個成員的部署所提供|聯盟中第一個成員的有效主要金鑰|NA

### <a name="summary"></a>總結

按一下 [摘要] 刀鋒視窗以檢閱指定的輸入，並執行基本的預先部署驗證。

![總結](./media/ethereum-deployment/summary.png)

檢閱法律和隱私權條款，然後按一下 [購買] 以進行部署。 如果部署具有多個區域或用於聯盟網路，則此範本會預先部署必要的 VPN 閘道，以支援與其他成員的網路連線。 閘道部署可能需要最多 45 到 50 分鐘。

## <a name="post-deployment-sanity-checks"></a>部署後的例行性檢查

### <a name="administrator-page"></a>管理員頁面

順利完成部署並已佈建所有資源後，您可以移至管理員頁面，檢視區塊鏈網路及對部署狀態執行例行性檢查。 管理員頁面的 URL 是負載平衡器的 DNS 名稱；它會出現在名為 ADMIN_SITE 的範本部署輸出區段中。

若要尋找此 URL，請選取已部署的資源群組。 然後，選取 [概觀]，並按一下 [部署] 正下方顯示成功數量的連結。

![資源群組概觀](./media/ethereum-deployment/resource-overview.png)

新的畫面會顯示部署記錄。 選取第一個部署資源 (例如 microsoft-azure-blockchain.azure-blockchain-servi...)，然後在頁面的下半部尋找 [輸出] 區段。 您會看到範本部署輸出參數 ADMIN_SITE 中列出管理員頁面的 URL。

![資源部署](./media/ethereum-deployment/resource-deployments.png)

![部署輸出](./media/ethereum-deployment/deployment-output.png)

若要前往管理員頁面，請複製 **ADMIN-SITE** 輸出，並在另一個索引標籤中將其開啟。

在管理員頁面中，您可以藉由檢閱以太坊節點狀態的區段，來取得所部署拓撲的整體概觀。 其中包含所有節點的主機名稱、同儕節點計數，以及最後可看見的區塊。 每個節點的同儕節點計數會介於最小值 (「節點總數」減一) 和設定的最大值之間。 請注意，同儕節點計數不會限制可部署在網路中的節點數目。
有時候，您會看到同儕節點計數變動且小於 (節點總數 - 1)。 計數的差異不一定是節點狀況不良的徵象，因為總帳中的分支可能會造成同儕節點計數有微幅變動。 最後，您可以檢查網路中每個節點最後可看見的區塊，以判斷系統中的分支和延隔時間。

![節點狀態](./media/ethereum-deployment/node-status.png)

節點狀態每隔 10 秒會重新整理。 透過瀏覽器或 [重新載入] 按鈕來重新載入頁面，以更新檢視。

### <a name="oms-portal"></a>OMS 入口網站

您可以透過部署輸出中的連結 (OMSPORTALURL)，或選取所部署資源群組中的 OMS 資源，來找出您的 OMS 入口網站。

![OMS URL](./media/ethereum-deployment/oms-url.png)

![OMS 資源](./media/ethereum-deployment/oms-resource.png)

入口網站會先顯示整體的網路統計資料概觀。

![OMS 概觀](./media/ethereum-deployment/oms-overview.png)

按一下 [概觀] 即會將您導向入口網站，讓您可檢視每個節點的統計資料。

![每個節點的統計資料](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>存取節點

您可以透過 SSH 及所提供的管理使用者名稱和密碼/ SSH 金鑰，從遠端連線到交易節點的虛擬機器。 由於交易節點 VM 不會有自己的公用 IP 位址，因此您必須通過負載平衡器並指定連接埠號碼。 範本部署輸出參數 **SSH_TO_FIRST_TX_NODE** 中會列出存取第一個交易節點所要執行的 SSH 命令 (部署範例：ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com)。 若要前往其他交易節點，則將通訊埠編號加上 1 (例如，第一個交易節點在連接埠 4000 上)。

由於執行採礦節點的虛擬機器不可從外部可存取，因此您必須通過其中一個交易節點。 在您透過 SSH 工作階段來連線到交易節點後，在交易節點上安裝您的私密金鑰，或啟動 SSH 工作階段來連線到任何採礦節點。

**注意**

您可以從管理員網站或 Azure 入口網站取得主機名稱。 在 Azure 入口網站中，存在於虛擬機器擴展集 (VMSS) 中的節點主機名稱會列在 [執行個體] 下方，這不同於實際主機名稱。 例如，Azure 入口網站中的主機名稱可能看起來像是 **mn-asdfmv-reg1_0**，但實際的主機名稱會像是 **mn-asdfmv-reg**。

例如︰

Azure 入口網站的主機名稱| 實際的主機名稱
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>新增聯盟成員

### <a name="sharing-data"></a>共用資料

身為聯盟第一個成員 (或已連線成員) 的您，需要提供其他成員一些資訊，讓他們可以加入和建立他們的連線。 具體而言：

1. **共用的聯盟設定資料**：一組資料，用來協調兩個成員之間的 Ethereum 連線。 必要資訊 (包括創世區塊、聯盟網路識別碼及啟動節點) 會寫入一個檔案中，此檔案位於領導者或另一位已部署成員的交易節點上。 此檔案的位置會列在名為 **CONSORTIUM-DATA** 的範本部署輸出參數中。
2. **同儕節點資訊端點**：同儕節點資訊端點可從領導者或另一個成員的部署中，取得已連線至 Ethereum 網路的所有節點資訊。 針對在網路中連線的每個節點，與其相關的一組資訊會存放在資料庫中，資訊包括節點的主機名稱、私人 IP 位址等。這是名為 **PEER_INFO_ENDPOINT** 的範本部署輸出參數。
3. **同儕節點資訊主要金鑰**：同儕節點資訊登錄器的主要金鑰可用來存取領導者或其他成員的同儕節點資訊主要金鑰。 這是名為 **PEER_INFO_PRIMARY_KEY** 的範本部署輸出參數。


4. **VNET 閘道**：每個成員都會透過現有成員來建立他們與整個區塊鏈的連線。 若要與 VNET 連線，您需要成員的 VNET 閘道資源路徑，才能與該成員連線。 這是名為 **CONSORTIUM_MEMBER_GATEWAY_ID** 的範本部署輸出參數。
5. **共用金鑰**：聯盟網路中建立連線的兩個成員之間所預先建立的祕密。 這是在部署環境外就已同意的英數字串 (介於 1 到 128 個字元之間)。 (例如 **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>接受新成員

加入的成員成功部署其網路之後，應該完成此步驟。 在成員可以加入網路並查看交易流量之前，現有的成員必須在 VPN 閘道上執行最後的設定，以接受連線。 這表示所加入成員的以太坊節點須在連線建立後才能執行。 此設定可透過 PowerShell 或 xPlat CLI 來完成。 PowerShell 模組和 xPlat CLI 指令碼也會與聯盟資料一起儲存在交易節點上。 指令碼位置分別是名為 **PAIR-GATEWAY-PS-MODULE** 和 **PAIR-GATEWAY-AZURE-CLISCRIPT** 的部署輸出參數。

**PowerShell/CLI 設定**

有關如何開始使用 Azure PowerShell Cmdlet 和 Azure xPlat CLI 的其他資訊可在 Azure 文件中取得。

您必須在本機安裝最新版的 Azure Cmdlet，並開啟一個工作階段。 請務必使用您的 Azure 訂用帳戶認證登入工作階段。

**PowerShell：建立連線**

下載 PowerShell 模組，並將它儲存在本機。 PowerShell 模組的位置會指定為 **PAIR-GATEWAY-PS-MODULE** 範本部署輸出參數。

如果尚未啟用，請針對本機工作階段使用 **Set-ExecutionPolicy** Cmdlet，以允許執行未簽署的模組。

**Set-ExecutionPolicy Unrestricted CurrentUser**

接下來，登入您在其中部署領導者部署的 Azure 訂用帳戶，請使用

**Login-AzureRmAccount**

下一步，匯入模組：

**Import-Module <filepath to downloaded file>**

最後，使用適當的輸入來執行函式：

- **MyGatewayResourceId：** 您閘道的資源路徑。 這是名為 **CONSORTIUM_MEMBER_GATEWAY_ID** 的範本部署輸出參數。
- **OtherGatewayResourceId：** 加入成員之閘道的資源路徑。 這會由所加入成員提供，而且也是名為 **CONSORTIUM_MEMBER_GATEWAY_ID** 的範本部署輸出參數。
- **ConnectionName：** 供您識別此閘道連線的名稱。
- **共用金鑰：** 為建立連線而在兩個聯盟網路成員間預先建立的祕密。

**CreateConnection** - MyGatewayResourceId <resource path of your Gateway> -OtherGatewayResourceId <所加入成員的閘道資源路徑> -ConnectionName myConnection -SharedKey "MySharedKeyAbc123"

**XPlat CLI：建立連線**

下載 Azure CLI 指令碼，並將它儲存在本機。 Azure CLI 指令碼的位置會在名為 **PAIR-GATEWAY-AZURE-CLI-SCRIPT** 的範本部署參數中指定。

使用適當的輸入執行指令碼：

- **MyGatewayResourceId：** 您閘道的資源路徑。 這是名為 **CONSORTIUM_MEMBER_GATEWAY_ID** 的範本部署輸出參數。
- **OtherGatewayResourceId：** 加入成員之閘道的資源路徑。 這會由所加入成員提供，而且是其部署的範本部署參數，也名為 **CONSORTIUM_MEMBER_GATEWAY_ID**。
- **ConnectionName：** 供您識別此閘道連線的名稱。
- **共用金鑰：** 為建立連線而在兩個聯盟網路成員間預先建立的祕密。
- **位置：** 您的閘道資源所部署在的 Azure 區域。

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

當您成功設定**領導者**和**成員**部署之間的連線後，其架構會如下所示。

![領導者和成員的架構](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>提供資金到新成員的帳戶

### <a name="generated-genesis-block"></a>產生的創世區塊

由於您是第一個成員，如果部署產生創世區塊 (自訂創世區塊 = 否)，則您的以太坊帳戶會有一兆枚以太幣作為資金。 其他成員的帳戶將不會有預先提供的資金，並且必須等到他們的採礦節點開始對區塊進行採礦時，才能累積以太幣。 若不要讓新成員等待累積乙太幣，您必須明確地提供基金到所加入成員的以太坊帳戶。

若要這樣做，所加入成員必須提供您他們管理員網站上顯示的以太坊帳戶。 接著，您可以使用管理員網站來輸入所提供的帳戶，將以太幣從您的帳戶轉到他們的帳戶。

### <a name="custom-genesis-block"></a>自訂創世區塊

如果提供的自訂創世區塊包含指定的以太坊帳戶，您可以使用 MetaMask 或另一個工具，將以太幣從指定帳戶轉送到預先產生的以太坊帳戶，您可以在管理員網站上看到該帳戶。 如需如何使用 MetaMask 的指示，請參閱[建立以太坊帳戶](#create-ethereum-account)。

如果提供的自訂創世區塊不含帳戶，或您無權存取任何預先配置的帳戶，則您必須等到採礦節點開始採礦，才能在您的帳戶中產生以太幣 (coin base)。 資金產生的速度取決於您在自訂創世區塊中指定的難度等級。

## <a name="create-ethereum-account"></a>建立以太坊帳戶

若要建立其他帳戶，您可以使用各種不同的解決方案。 這類解決方案的其中一個是 MetaMask，它是 Chrome 的擴充功能，可提供身分識別保存庫，以及與以太坊網路、公用、測試或自訂環境的連線。 MetaMask 會制定交易以在網路中註冊帳戶。

如同任何其他交易，這筆交易會移至其中一個交易節點，並最後採礦至區塊，如下所示。

![交易節點](./media/ethereum-deployment/transaction-node.png)

若要在 Chrome 中安裝擴充功能，請移至 [自訂和控制 Google Chrome] (溢位按鈕) > [更多工具] > [擴充功能] > [取得更多擴充功能]，然後搜尋 MetaMask。

![MetaMask 擴充功能](./media/ethereum-deployment/metamask-extension.png)

安裝之後，請開啟 MetaMask 並建立新的保存庫。 根據預設，保存庫會連線到新式測試網路中。 請將其變更為連線到私人聯盟網路，特別是連線到交易節點前方的負載平衡器。 在範本輸出中，擷取連接埠 8545 上公開的以太坊 RPC 端點 (名為 `ETHEREUM-RPC-ENDPOINT`)，並將其輸入至自訂的 RPC，如下所示。

![MetaMask 設定](./media/ethereum-deployment/metamask-settings.png)

藉由建立保存庫，您可以建立包含帳戶的錢包。 若要建立其他帳戶，請選取 [切換帳戶]，然後按一下 [+] 按鈕。

![MetaMask 建立帳戶](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>開始初次的以太幣配置

透過管理員頁面，您可以編制交易來將以太幣從預先配置的帳戶轉到另一個以太坊帳戶。 此以太幣轉送的交易是傳送至交易節點，並採礦到區塊，如下所示。

![交易節點](./media/ethereum-deployment/transaction-node-mined.png)

透過 MetaMask 錢包中的 [剪貼簿] 圖示，複製您想要對其傳送以太幣的以太坊帳戶位址，然後返回管理員頁面。 將複製的帳戶貼到輸入欄位，並將 1000 枚以太幣從預先配置的以太坊帳戶轉到新建立的帳戶。 按一下 [送出]，並等候交易採礦到區塊。

![節點狀態](./media/ethereum-deployment/node-status2.png)

一旦交易在採礦的區塊中受到認可後，在您帳戶的 MetaMask 中，帳戶餘額將會反映 1000 枚以太幣的轉帳。

![MetaMask 轉帳](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>帳戶間的以太幣轉帳

此時，您已可以在私人聯盟網路中執行交易。 最簡單的交易就是將以太幣從一個帳戶轉到另一個帳戶。 若要編制這類交易，您可以再次使用 MetaMask，將金錢從上方使用的第一個帳戶轉到第二個帳戶。

從 MetaMask 中的 [錢包 1]，按一下 [傳送]。 複製 [收件者位址] 輸入欄位中建立的第二個錢包帳戶，以及在 [金額] 輸入欄位中輸入要轉出的以太幣金額。 按一下 [傳送] 並接受交易。

![MetaMask 傳送交易](./media/ethereum-deployment/metamask-send.png)

同樣地，當交易進行採礦並認可至區塊後，帳戶餘額會隨之反映結果。 請注意，[錢包 1] 餘額中減少的以太幣會超過 15 枚，因為您必須支付處理交易的採礦費用。

![錢包 1](./media/ethereum-deployment/wallet1.png)

![錢包 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>後續步驟

您現在可以將焦點放在如何透過您的私人聯盟區塊鏈網路，來進行應用程式和智慧合約開發。
