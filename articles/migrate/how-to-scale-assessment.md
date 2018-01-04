---
title: "使用 Azure 移轉縮放探索及評估 |Microsoft 文件"
description: "描述如何使用 Azure 移轉的服務，以評估大量內部部署機器。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/19/2017
ms.author: raynew
ms.openlocfilehash: 9b457252fdb7a1ad62b7e6038b341451df2e1590
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>探索及評定大型 VMware 環境

這篇文章描述如何使用大量的內部部署虛擬機器 (Vm)，評估[Azure 移轉](migrate-overview.md)。 Azure 的移轉，評估電腦來檢查它們是否適用於移轉至 Azure。 此服務會提供在 Azure 中執行之機器的大小且符合成本估計。

## <a name="prerequisites"></a>必要條件

- **VMware**： 您規劃移轉的 Vm 必須由 vCenter Server 5.5、 6.0 或 6.5 版本。 此外，您需要一個 ESXi 主機正在執行版本 5.0 或更新版本來部署 VM，收集器。
- **vCenter 帳戶**： 您需要唯讀存取 vCenter Server 帳戶。 Azure Migrate 會使用此帳戶來探索內部部署 VM。
- **權限**： 在 vCenter Server 中，您需要匯入 OVA 格式的檔案來建立 VM 的權限。
- **統計資料設定**: vCenter Server 的統計資料設定應該設定為層級 3，在開始部署之前。 如果層級低於 3，評估也能運作，但不會收集效能資料的儲存體和網路。 建議的大小在此情況下將會根據 CPU 和記憶體的效能資料和磁碟與網路介面卡的組態資料。

## <a name="plan-azure-migrate-projects"></a>規劃 Azure Migrate 專案

規劃您的探索及評估在下列限制：

| **實體** | **電腦限制** |
| ---------- | ----------------- |
| 隨附此逐步解說的專案    | 1,500              | 
| 探索  | 1,000              |
| 評量 | 400               |

- 如果您有超過 400 的機器探索及評估，您需要在單一專案中，單一探索。 根據您的需求，您可以評估單一評估中的所有機器，或將機器分割成多個評估。 
- 如果您有 400 至 1,000 機器探索，您需要在單一專案中使用單一的探索。 但是，您需要多個評估來評估這些機器，因為在單一評估可以保存多達 400 的機器。
- 如果您有要 1500 1001 機器，您需要在單一專案中使用中它的兩個探索。
- 如果您有超過 1,500 個機器，您需要建立多個專案，並執行多個探索，根據您的需求。 例如︰
    - 如果您有 3000 的機器，您可以設定兩個專案與兩個探索或使用單一探索的三個專案。
    - 如果您有 5,000 機器，您可以設定四個專案： 兩個 1500 的機器探索，另一個有 500 機器的探索。 或者，您可以設定五個專案中每一個單一的探索。 

## <a name="plan-multiple-discoveries"></a>規劃多個探索

您可以使用相同的 Azure 移轉收集器執行多個探索，以一個或多個專案。 請注意下列規劃考量：
 
- 當您使用 Azure 移轉的收集器進行探索時，您可以設定探索範圍以 vCenter 伺服器資料夾、 資料中心、 叢集或主機。
- 若要執行一個以上的探索，請確認 vCenter 伺服器，您想要探索的 Vm 位於資料夾、 資料中心、 叢集或支援的限制 1,000 機器的主機中。
- 我們建議您基於評估目的，您保留在相同的專案和評估相互依存性的機器。 在 vCenter Server，請確定相依的機器都處於相同的資料夾、 資料中心或評估的叢集。


## <a name="create-a-project"></a>建立專案

建立 Azure 移轉專案根據您的需求：

1. 在 Azure 入口網站中，選取**建立資源**。
2. 搜尋**Azure 移轉**，並選取服務**Azure 移轉 （預覽）**搜尋結果中。 然後選取 [建立]。
3. 指定專案名稱和專案的 Azure 訂用帳戶。
4. 建立新的資源群組。
5. 指定您要建立專案，然後選取的位置**建立**。 請注意，您仍可以評估不同的目標位置的 Vm。 指定給專案的位置用來儲存在內部部署的 Vm 所傳來蒐集的中繼資料。

## <a name="set-up-the-collector-appliance"></a>設定收集器設備

Azure Migrate 會建立稱為「收集器設備」的內部部署 VM。 此 VM 會探索在內部部署 VMware Vm，和其相關的中繼資料傳送至 Azure 移轉的服務。 若要設定收集器的應用裝置，您可以下載 OVA 檔案，並將它匯入內部 vCenter 伺服器執行個體。

### <a name="download-the-collector-appliance"></a>下載收集器設備

如果您有多個專案，您需要下載到 vCenter Server 的行程應用裝置一次。 下載並設定裝置之後，會為每個專案中，執行，並且指定唯一的專案識別碼和金鑰。

1. 在 Azure 移轉專案中，選取**入門** > **探索及評估** > **探索機器**。
2. 在**探索機器**，選取**下載**、 下載 OVA 檔案。
3. 在 [複製專案認證] 中，複製專案識別碼和索引鍵。 在設定收集器時，您會需要這些資料。

   
### <a name="verify-the-collector-appliance"></a>確認收集器設備

OVA 檔案是安全的在部署前的檢查：

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 的雜湊：

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 請確定產生的雜湊比對下列設定。
 
    若為 1.0.8.38 OVA 版本：
    **演算法** | **雜湊值**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    若為 1.0.8.40 OVA 版本：
    **演算法** | **雜湊值**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad



## <a name="create-the-collector-vm"></a>建立收集器 VM

將下載的檔案匯入 vCenter Server:

1. 在 vSphere 用戶端主控台中，選取**檔案** > **部署 OVF 範本**。

    ![部署 OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. 在 部署 OVF 範本精靈 >**來源**，指定 OVA 檔案的位置。
3. 在 [名稱] 和 [位置] 中，指定收集器 VM 的易記名稱，以及要裝載 VM 的所在清查物件。
5. 在 [主機/叢集] 中，指定收集器 VM 的執行所在主機或叢集。
7. 在儲存體中，指定收集器 VM 的儲存目的地。
8. 在 [磁碟格式] 中，指定磁碟類型和大小。
9. 在 [網路對應] 中，指定收集器 VM 所要連線的網路。 網路需要網際網路連線來傳送至 Azure 的中繼資料。 
10. 檢閱並確認設定，然後選取**完成**。

## <a name="identify-the-id-and-key-for-each-project"></a>識別的識別碼和索引鍵的每個專案

如果您有多個專案，請務必識別的識別碼，並針對每個索引鍵。 當您執行收集器來探索 VM 時，將會需要索引鍵。

1. 在專案中，選取**入門** > **探索及評估** > **探索機器**。
2. 在 [複製專案認證] 中，複製專案識別碼和索引鍵。 
    ![複製專案認證](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>設定 vCenter 統計資料層級
以下是會在探索期間收集的效能計數器的清單。 計數器是由在 vCenter Server 中的各種層級預設值。 

我們建議您設定的統計資料層級的最高一般層級 (3)，以便正確收集所有計數器。 如果您有 vCenter 較低層級設定，只有少數計數器可能會設定為 0 的其餘部分完整地收集。 評估然後可能會顯示不完整的資料。 

下表也列出如果特定的計數器不會收集將會受到影響的評估結果。

|計數器                                  |Level    |每一裝置層級  |評定影響                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |NA                |建議的 VM 大小和成本                    |
|mem.usage.average                        | 1       |NA                |建議的 VM 大小和成本                    |
|virtualDisk.read.average                 | 2       |2                 |磁碟大小、 儲存體成本和 VM 大小         |
|virtualDisk.write.average                | 2       |2                 |磁碟大小、 儲存體成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |磁碟大小、 儲存體成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average  | 1       |3                 |磁碟大小、 儲存體成本和 VM 大小         |
|net.received.average                     | 2       |3                 |VM 大小和網路成本                        |
|net.transmitted.average                  | 2       |3                 |VM 大小和網路成本                        |

> [!WARNING]
> 如果您已經設定較高的統計資料，它最多需要一天產生的效能計數器。 因此，我們建議您在一天後執行探索。

## <a name="run-the-collector-to-discover-vms"></a>執行收集器來探索 VM

您需要執行每個探索，您會執行收集器來探索 Vm 中必要的範圍。 請逐一執行探索。 系統不支援並行探索，且每個探索的範圍不得相同。

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]。
2. 提供設備的語言、時區和密碼喜好設定。
3. 在桌面上，選取**執行收集器**捷徑。
4. 在 Azure 移轉行程中，開啟**設定先決條件**然後：

   a. 接受授權條款，並閱讀第三方資訊。

   收集器會確認 VM 是否能夠存取網際網路。
   
   b. 如果 VM 存取網際網路，透過 proxy，選取**Proxy 設定**，並指定 proxy 位址和接聽連接埠。 如果 Proxy 需要驗證，請指定認證。

   收集器會檢查確認收集器服務正在執行。 根據預設，收集器 VM 上會安裝此服務。

   c. 下載並安裝 VMware PowerCLI。

5. 在**指定 vCenter 伺服器詳細資料**，執行下列動作：
    - 指定的名稱 (FQDN) 或 IP 位址的 vCenter Server。
    - 在**使用者名**和**密碼**，指定收集器將用於探索 vCenter 伺服器中 Vm 的唯讀狀態的帳戶認證。
    - 在**選取範圍**，選取 VM 探索的領域。 收集器可以探索指定的範圍內的 Vm。 範圍可以設定為特定資料夾、資料中心或叢集。 它不應該包含超過 1,000 個 Vm。 
    - 在**vCenter 標記類別來分組**，選取**無**。

    ![選取範圍](./media/how-to-scale-assessment/select-scope.png)

6. 在**指定移轉專案**、 指定 ID 和重要的專案。 如果您沒有複製它們，開啟 Azure 入口網站之 VM 的收集器。 在專案上**概觀**頁面上，選取**探索機器**，並將複製的值。  
7. 在**檢視收集進度**，監視探索程序，並檢查在 Vm 中收集的中繼資料不在範圍內。 收集器會提供概略的探索時間。


### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索時間取決於您要探索多少 VM。 一般來說，對於 100 的 Vm，探索完成周圍收集器完成執行後的一小時。 

1. 在規劃移轉專案中，選取**管理** > **機器**。
2. 確認您想要探索的 VM 出現在入口網站內。


## <a name="next-steps"></a>後續步驟

- 了解如何[建立評定群組](how-to-create-a-group.md)。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
