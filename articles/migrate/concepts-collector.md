---
title: Azure Migrate 中的收集器設備 | Microsoft Docs
description: 提供收集器設備的概觀及其設定方式。
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: c99d0f74dbb8cc28cabebae60fe10645f4bdb3b6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308454"
---
# <a name="collector-appliance"></a>收集器設備

[Azure Migrate](migrate-overview.md) 會評估要移轉至 Azure 的內部部署工作負載。 本文提供如何使用收集器設備的相關資訊。



## <a name="overview"></a>概觀

Azure Migrate 收集器是輕量型設備，可用來探索您的內部部署 vCenter 環境。 此設備會探索內部部署 VMware 機器，並將其相關中繼資料傳送至 Azure Migrate 服務。

收集器設備是您可以從 Azure Migrate 專案下載的 OVF。 它會將包含 4 個核心、8 GB RAM 和一個 80 GB 磁碟的 VMware 虛擬機器具現化。 設備的作業系統是 Windows Server 2012 R2 (64 位元)。

您可以依照下列步驟來建立收集器 - [如何建立收集器 VM](tutorial-assessment-vmware.md#create-the-collector-vm)。

## <a name="collector-communication-diagram"></a>收集器通訊圖表

![收集器通訊圖表](./media/tutorial-assessment-vmware/portdiagram.PNG)


| 元件      | 通訊對象   | 所需的連接埠                            | 原因                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| 收集器      | Azure Migrate 服務 | TCP 443                                  | 收集器應該能夠透過 SSL 連接埠 443 來與服務通訊 |
| 收集器      | vCenter Server        | 預設值 443                             | 收集器應該能夠與 vCenter Server 通訊。 它預設會透過 443 與 vCenter 連線。 如果 vCenter 接聽不同的連接埠，則該連接埠應該作為收集器上的傳出連接埠 |
| 收集器      | RDP|   | TCP 3389 | 使您可以透過 RDP 連線到收集器電腦 |





## <a name="collector-pre-requisites"></a>收集器的必要條件

收集器必須通過幾個必要條件檢查，以確保它可以連線到 Azure Migrate 服務並上傳探索到的資料。 本文會查看每個必要條件並了解需要的原因。

### <a name="internet-connectivity"></a>網際網路連線

收集器設備必須連線到網際網路，以傳送探索到的機器資訊。 您可以用下列兩種方式之一將機器連線到網際網路。

1. 您可以將收集器設定為具有直接網際網路連線。
2. 您可以將收集器設定為透過 proxy 伺服器連線。
    * 如果 proxy 伺服器需要驗證，您可以在連線設定中指定使用者名稱和密碼。
    * Proxy 伺服器的 IP 位址/FQDN 形式必須為 http://IPaddress 或 http://FQDN。 僅支援 HTTP Proxy。

> [!NOTE]
> 收集器不支援 HTTPS 型 Proxy 伺服器。

#### <a name="whitelisting-urls-for-internet-connection"></a>將網際網路連線的 URL 加入白名單

如果收集器可以透過所提供的設定連線到網際網路，必要條件檢查就會成功。 您可以連線到下表中所提供的 URL 清單來驗證連線檢查。 如果您使用任何 URL 型防火牆 Proxy 控制輸出連線能力，務必將這些必要的 URL 列入允許清單：

**URL** | **用途**  
--- | ---
*.portal.azure.com | 檢查與 Azure 服務的連線能力及驗證時間同步問題時所需。

此外，檢查也會嘗試驗證下列 URL 的連線，但如果無法存取，檢查也不會失敗。 您可以選擇性地設定下列 URL 的允許清單，但您必須採取手動步驟來減輕必要條件檢查。

**URL** | **用途**  | **如果不加入允許清單該怎麼辦**
--- | --- | ---
*.oneget.org:443 | 下載以 Powershell 為基礎的 vCenter PowerCLI 模組時所需。 | PowerCLI 安裝失敗。 手動安裝模組。
*.windows.net:443 | 下載以 Powershell 為基礎的 vCenter PowerCLI 模組時所需。 | PowerCLI 安裝失敗。 手動安裝模組。
*.windowsazure.com:443 | 下載以 Powershell 為基礎的 vCenter PowerCLI 模組時所需。 | PowerCLI 安裝失敗。 手動安裝模組。
*.powershellgallery.com:443 | 下載以 Powershell 為基礎的 vCenter PowerCLI 模組時所需。 | PowerCLI 安裝失敗。 手動安裝模組。
*.msecnd.net:443 | 下載以 Powershell 為基礎的 vCenter PowerCLI 模組時所需。 | PowerCLI 安裝失敗。 手動安裝模組。
*.visualstudio.com:443 | 下載以 Powershell 為基礎的 vCenter PowerCLI 模組時所需。 | PowerCLI 安裝失敗。 手動安裝模組。

### <a name="time-is-in-sync-with-the-internet-server"></a>時間與網際網路伺服器同步

收集器應該與網際網路時間伺服器保持同步，以確保會驗證服務的要求。 您應該可以從收集器觸達 portal.azure.com url，以便驗證時間。 如果機器未同步，您必須變更收集器 VM 的時間，使之與目前時間相符，如下所示：

1. 在虛擬機器上開啟系統管理命令提示字元。
1. 若要檢查時區，請執行 w32tm /tz。
1. 若要同步時間，請執行 w32tm /resync。

### <a name="collector-service-should-be-running"></a>收集器服務應在執行中

Azure Migrate 收集器服務應在機器上執行。 此服務會在機器開機時自動啟動。 如果服務未執行，您可以透過控制台來啟動 Azure Migrate 收集器服務。 收集器服務會負責連線到 vCenter Server、收集機器中繼資料和效能資料，並將資料傳送給服務。

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

您必須安裝 VMware PowerCLI powershell 模組，收集器才能與 vCenter Server 通訊，以及查詢機器詳細資料及其效能資料。 進行必要條件檢查時，系統會自動下載及安裝 Powershell 模組。 自動下載必須將少數 URL 加入白名單中，若未這麼做，您必須將它們加入白名單來提供存取，或以手動方式安裝模組。

使用下列步驟以手動方式安裝模組：

1. 若要在無網際網路連線的情況下在收集器上安裝 PowerCli，請依照[此連結](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)提供的步驟進行。
2. 一旦在不同的電腦 (具有網際網路存取) 上安裝 PowerShell 模組後，請將 VMware.* 檔案從該機器複製到收集器機器。
3. 重新啟動必要條件檢查，並確認已安裝 PowerCLI。

## <a name="connecting-to-vcenter-server"></a>連線到 vCenter Server

收集器應連線到 vCenter Server，而且能夠查詢虛擬機器、其中繼資料及其效能計數器。 專案會使用這項資料來計算評量。

1. 若要連線至 vCenter Server，可以使用具有下表中所提供權限的唯讀帳戶來執行探索。

    |Task  |必要的角色/帳戶  |權限  |
    |---------|---------|---------|
    |以收集器設備為基礎的探索    | 您需要至少一個唯讀使用者        |資料中心物件 –> 傳播至子物件、role=Read-only         |

2. 只能存取指定的 vCenter 帳戶可存取的這些資料中心進行探索。
3. 您必須指定要連線至 vCenter Server 的 vCenter FQDN/IP 位址。 根據預設，它會透過連接埠 443 連線。 如果您已將 vCenter 設定為在不同的連接埠號碼上接聽，可以用 IPAddress:Port_Number 或 FQDN:Port_Number 格式將它指定為伺服器位址的一部分。
4. vCenter Server 的統計資料設定應該先設為層級 3，然後再開始部署。 如果層級低於 3，還是能完成探索，但不會收集儲存體和網路的效能資料。 在此情況下，將根據 CPU 和記憶體的效能資料，以及只有磁碟和網路介面卡的組態資料來提出評量大小建議。 [深入了解](./concepts-collector.md)所收集的資料，以及它對評量有何影響。
5. 收集器應該能透過網路看到 vCenter Server。

> [!NOTE]
> 僅正式支援 vCenter Server 5.5、6.0 和 6.5 版。

> [!IMPORTANT]
> 我們建議您為統計資料層級設定最高的一般層級 (3)，以正確收集所有計數器。 如果您的 vCenter 設定為較低層級，只有幾個計數器能完整收集，其他會設定為 0。 因此評定可能會顯示不完整的資料。

### <a name="selecting-the-scope-for-discovery"></a>選取探索的範圍

連線到 vCenter 後，您可以選取要探索的範圍。 選取範圍，即可探索指定的 vCenter 清查路徑中的所有虛擬機器。

1. 此範圍可以是資料中心、資料夾或 ESXi 主機。
2. 您一次只能選取一個範圍。 若要選取多部虛擬機器，您可以完成一次探索，然後以新的範圍重新啟動探索程序。
3. 您只能選取「少於 1500 部虛擬機器」的範圍。

## <a name="specify-migration-project"></a>指定移轉專案

一旦連線內部部署 vCenter 並指定範圍後，您現在可以指定必須用於探索及評量的移轉專案詳細資料。 指定專案識別碼和金鑰，然後進行連線。

## <a name="start-discovery-and-view-collection-progress"></a>開始探索並檢視收集進度

開始探索後，就會發現 vCenter 虛擬機器，且其中繼資料和效能資料會傳送到伺服器。 進度狀態也會告知您下列識別碼：

1. 收集器識別碼：提供給收集器機器的唯一識別碼。 此識別碼不會針對不同探索中的特定機器而變更。 在發生失敗的情況下，您可以使用這個識別碼向 Microsoft 支援服務回報問題。
2. 工作階段識別碼：執行中收集作業的唯一識別碼。 探索作業完成時，您可以在入口網站中參考相同的工作階段識別碼。 此識別碼會隨著每個收集作業而變更。 在發生失敗的情況下，您可以向 Microsoft 支援服務回報此識別碼。

### <a name="what-data-is-collected"></a>收集了哪些資料？

收集作業會探索下列關於所選虛擬機器的靜態中繼資料。

1. VM 顯示名稱 (在 vCenter 上)
2. VM 的清查路徑 (vCenter 中的主機/資料夾)
3. IP 位址
4. MAC 位址
5. 作業系統
5. 核心、磁碟、NIC 數目
6. 記憶體大小、磁碟大小
7. 以及 VM、磁碟及網路的效能計數器，如下表所列。

下表列出已收集的效能計數器，還列出未收集特定計數器時受到影響的評量結果。

|計數器                                  |Level    |每一裝置層級  |評定影響                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |NA                |建議的虛擬機器大小和成本                    |
|mem.usage.average                        | 1       |NA                |建議的虛擬機器大小和成本                    |
|virtualDisk.read.average                 | 2       |2                 |磁碟大小、儲存成本和虛擬機器大小         |
|virtualDisk.write.average                | 2       |2                 |磁碟大小、儲存成本和虛擬機器大小         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |磁碟大小、儲存成本和虛擬機器大小         |
|virtualDisk.numberReadAveraged.average  | 1       |3                 |磁碟大小、儲存成本和虛擬機器大小         |
|net.received.average                     | 2       |3                 |虛擬機器大小和網路成本                        |
|net.transmitted.average                  | 2       |3                 |虛擬機器大小和網路成本                        |

> [!WARNING]
> 如果您剛剛設定較高的統計資料層級，可能需要一天的時間才能產生效能計數器。 因此，建議您在一天後執行探索。

### <a name="time-required-to-complete-the-collection"></a>完成收集所需要的時間

收集器只會探索機器資料，並將資料傳送至專案。 專案可能需要額外的時間，探索到的資料才會顯示在入口網站中，而您才能開始建立評量。

根據所選範圍中的虛擬機器數目，最多需花費 15 分鐘才能將靜態中繼資料傳送至專案。 一旦中繼資料可用於入口網站，您就可以在入口網站中看到機器清單並開始建立群組。 直到收集作業完成且專案處理資料後，才能建立評量。 收集作業在收集器上完成後，根據所選範圍中的虛擬機器數目，最多可能需要一小時，才能在入口網站取得效能資料。

## <a name="locking-down-the-collector-appliance"></a>鎖定收集器設備
我們建議在收集器設備上持續執行 Windows 更新。 如果收集器 60 天未更新，則收集器會開始自動關閉機器。 如果正在執行探索，即使機器已超過 60 天未更新，也不會關閉機器。 張貼探索工作完成，將會關閉機器。 如果您使用收集器超過 45 天，建議執行 Windows 更新以隨時更新機器。

我們也建議下列步驟以保護您的設備
1. 請勿與未經授權的人員分享或錯置管理員密碼。
2. 不使用時，關閉設備。
3. 將設備放置在安全的網路中。
4. 移轉工作完成之後，請刪除應用裝置執行個體。 請務必也刪除磁碟備份檔案 (VMDK)，因為磁碟可能會在這些檔案上快取 vCenter 認證。

## <a name="how-to-upgrade-collector"></a>如何升級收集器

您可以在不需再次下載 OVA 的情況下，將收集器升級為最新版本。

1. 下載最新的[升級套件](https://aka.ms/migrate/col/upgrade_9_13) (1.0.9.13 版)。
2. 若要確保下載的 Hotfix 是安全的，請開啟系統管理員命令視窗並執行下列命令來產生 ZIP 檔案的雜湊。 產生的雜湊應該符合針對特定版本所述的雜湊：

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (使用範例：C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.7.zip SHA256)
3. 將 ZIP 檔案複製到 Azure Migrate 收集器虛擬機器 (收集器設備)。
4. 以滑鼠右鍵按一下 ZIP 檔案並選取 [全部解壓縮]。
5. 以滑鼠右鍵按一下 Setup.ps1 並選取 [使用 PowerShell 執行]，然後遵循螢幕上的指示安裝更新。

### <a name="list-of-updates"></a>更新清單

#### <a name="upgrade-to-version-10913"></a>升級為 1.0.9.13 版

升級[套件 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13) 的雜湊值

**演算法** | **雜湊值**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

#### <a name="upgrade-to-version-10911"></a>升級為 1.0.9.11 版

升級[套件 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11) 的雜湊值

**演算法** | **雜湊值**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>升級為 1.0.9.7 版

升級[套件 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7) 的雜湊值

**演算法** | **雜湊值**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>後續步驟

[為內部部署 VMware VM 設定評量](tutorial-assessment-vmware.md)
