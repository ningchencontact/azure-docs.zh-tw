---
title: "透過 Azure Migrate 使用機器相依性分組機器 | Microsoft Docs"
description: "說明如何透過 Azure Migrate 服務使用機器相依性來建立評量。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/25/2017
ms.author: raynew
ms.openlocfilehash: 720380fd14d9eaf4856ad75269a80f2b63a4725f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>使用機器相依性對應分組機器

本文說明如何建立電腦群組[Azure 移轉](migrate-overview.md)評定所視覺化的機器相依性。 在執行評量前，若想要透過交叉檢查機器相依性的方式，對信心等級較高的虛擬機器群組進行評估，通常會使用此方法。 相依性的視覺效果可協助您有效地規劃移轉至 Azure。 它可協助您確保執行任何動作就會遺留，與您要移轉至 Azure 時不會發生意外的中斷。 您可以探索需要一起移轉，並識別是否執行中的系統仍然會提供使用者或適合用於解除委任，而不移轉的所有交互相依系統。 


## <a name="prepare-machines-for-dependency-mapping"></a>準備機器以進行相依性對應
若要檢視相依性的電腦，您需要下載並安裝您想要評估每個內部部署電腦上的代理程式。 此外，如果您的機器沒有網際網路連線，則需要下載並安裝 [OMS 閘道](../log-analytics/log-analytics-oms-gateway.md)。

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
4. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics (OMS)] > [下一步]。 
5. 按一下 [新增] 以新增新的 OMS 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步] 。


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
2. 搜尋安裝代理程式的電腦。
3. **相依性**機器的資料行現在應顯示為**檢視相依性**。 按一下要檢視電腦的相依性的資料行。
4. 相依性對應機器會顯示下列詳細資料：
    - 輸入 （用戶端） 和輸出 （伺服器） TCP 連線至 azure 或從電腦
        - 不需要安裝 MMA 和相依性代理程式的相依機器乃依連接埠號碼
        - 具有 MMA 和相依性代理程式安裝的 dependenct 機器會顯示為不同的箱子 
    - 機器內執行的處理程序，您可以展開每個機器方塊，以檢視處理程序
    - 屬性，例如完整網域名稱、 作業系統、 每部機器的 MAC 位址等，您可以按一下每個機器方塊，以檢視這些詳細資料

 ![檢視電腦的相依性](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 您可以查看相依性不同的時間期間按一下時間範圍標籤中的持續時間。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。
5. 找出您想要分組在一起的相依機器之後，使用 Ctrl + 按一下以選取多部電腦，在地圖上，按一下**機器群組**。
6. 指定群組名稱。 請確認探索相依的機器時，會由 Azure 移轉。 

    > [!NOTE]
    > 如果相依機器未探索到 Azure 的移轉，您無法將其加入群組。 若要加入至群組的這類機器，您需要使用正確的範圍在 vCenter Server 中再次執行探索程序，並確定 Azure 移轉探索電腦。  

7. 如果您想要建立此群組評估，請選取 建立新的評估群組的核取方塊。
8. 按一下 [確定] 以儲存群組。

一旦建立群組之後，建議您將群組的所有機器上安裝代理程式和視覺化整個群組的相依性，以進一步修訂的群組。

## <a name="next-steps"></a>後續步驟

- [深入了解如何](how-to-create-group-dependencies.md)來精簡群組所視覺化群組相依性
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
