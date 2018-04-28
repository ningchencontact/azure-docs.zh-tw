---
title: 透過 Azure Migrate 使用機器相依性分組機器 | Microsoft Docs
description: 說明如何透過 Azure Migrate 服務使用機器相依性來建立評量。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/25/2017
ms.author: raynew
ms.openlocfilehash: 9e8e1fb918d9cd35b12e71abdd23ca101239ae26
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>使用機器相依性對應分組機器

本文說明如何將機器相依性視覺化，建立機器群組以進行 [Azure Migrate](migrate-overview.md) 評量。 在執行評量前，若想要透過交叉檢查機器相依性的方式，對信心等級較高的虛擬機器群組進行評估，通常會使用此方法。 相依性視覺效果可協助您有效地規劃移轉至 Azure。 它可協助您確保不會遺留任何動作，且在您要移轉至 Azure 時，不會發生意外的中斷。 您可以探索所有需要一起移轉的交互相依系統，以及識別執行中的系統是否仍為使用者提供服務，還是應該解除委任而非移轉。 


## <a name="prepare-machines-for-dependency-mapping"></a>準備機器以進行相依性對應
若要檢視機器的相依性，在待評估的每個內部部署機器上，您必須下載及安裝代理程式。 此外，如果您的機器沒有網際網路連線，則需要下載並安裝 [OMS 閘道](../log-analytics/log-analytics-oms-gateway.md)。

### <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式
1. 在 [概觀] 中，按一下 [管理] > [機器]，並選取所需的機器。
2. 在 [相依性] 資料行中，按一下 [安裝代理程式]。 
3. 至 [相依性] 頁面，下載 Microsoft Monitoring Agent (MMA) 及相依性代理程式，安裝到要進行評估的每個虛擬機器上。
4. 複製工作區識別碼與金鑰。 在內部部署機器上安裝 MMA 時，需要用到這些識別碼與金鑰。

### <a name="install-the-mma"></a>安裝 MMA

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3. 在 [目的地資料夾] 中，保留或修改預設的安裝資料夾 > [下一步]。 
4. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics] > [下一步]。 
5. 按一下 [新增] 以新增 Log Analytics 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步] 。


在 Linux 電腦上安裝代理程式：

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>安裝相依性代理程式
1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

[深入了解](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)相依性代理程式支援的作業系統。 

## <a name="create-a-group"></a>建立群組

1. 安裝代理程式之後，請移至入口網站，然後按一下[管理] > [機器]。
2. 搜尋安裝代理程式的機器。
3. 機器的 [相依性] 資料行現在應會顯示為**檢視相依性**。 按一下資料行以檢視機器的相依性。
4. 機器的相依性對應會顯示下列詳細資料：
    - 往返機器的輸入 (用戶端) 和輸出 (伺服器) TCP 連線
        - 未安裝 MMA 和相依性代理程式的相依機器會依連接埠號碼群組
        - 已安裝 MMA 和相依性代理程式的相依機器會以不同的方塊顯示 
    - 在機器內執行的處理序，您可以展開每個機器方塊，以檢視處理序
    - 每部機器的完整網域名稱、作業系統、MAC 位址等屬性，您可以按一下每個機器方塊來檢視這些詳細資料

 ![檢視電腦相依性](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 您可以按一下時間範圍標籤中的持續時間，以查看不同持續時間的相依性。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。
5. 找出您需要劃為同一群組的相依機器後，請使用 Ctrl + 按一下來選取對應中的多部機器，然後按一下 [群組機器]。
6. 指定群組名稱。 確認 Azure Migrate 已探索到相依機器。 

    > [!NOTE]
    > 如果 Azure Migrate 未探索到相依機器，您就無法將它新增至群組。 若要將這類機器新增至群組，您必須使用 vCenter Server 中的正確範圍，再次執行探索程序，並確定 Azure Migrate 已探索到機器。  

7. 如果您需要建立此群組的評估，請選取核取方塊來建立新的群組評估。
8. 按一下 [確定] 以儲存群組。

一旦建立群組之後，建議您在群組的所有機器上安裝代理程式，並將整個群組的相依性視覺化來調整群組。

## <a name="next-steps"></a>後續步驟

- [了解如何](how-to-create-group-dependencies.md)將群組相依性視覺化來調整群組
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
