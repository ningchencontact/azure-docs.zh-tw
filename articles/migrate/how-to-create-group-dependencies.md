---
title: 在 Azure Migrate 中使用群組相依性對應調整評量群組 | Microsoft Docs
description: 說明如何在 Azure Migrate 服務中使用群組相依性對應調整評量。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/22/2017
ms.author: raynew
ms.openlocfilehash: 3b10765894501791004e3a9221363f196cc0c91d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>使用群組相依性對應調整群組

本文描述如何藉由將群組中所有機器的相依性視覺化來調整群組。 當您想要為現有群組調整成員資格時，通常會使用此方法，先交叉檢查群組相依性，然後再進行評量。 使用相依性視覺效果來調整群組，可協助您有效地規劃移到 Azure 的移轉。您可以探索需要一起移轉的所有相依系統。 它可協助您確保不會遺留任何項目，且當您在移轉至 Azure 時，不會發生意外的中斷。 


> [!NOTE]
> 您想要將相依性視覺化的群組不應該包含超過 10 部的機器。 如果群組中有超過 10 部的機器，我們建議您將它分割成較小的群組，以利用相依性視覺效果功能。


# <a name="prepare-the-group-for-dependency-visualization"></a>為相依性視覺效果準備群組
若要檢視群組的相依性，您需要下載代理程式，並將它安裝在屬於群組的每個內部部署機器上。 此外，如果您的機器沒有網際網路連線，則需要下載並安裝 [OMS 閘道](../log-analytics/log-analytics-oms-gateway.md)。

### <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式
1. 在 [概觀] 中，按一下 [管理] > [群組]，移至必要的群組。
2. 在機器清單中，在 [相依性代理程式] 欄中，按一下 [需要安裝] 以查看有關如何下載並安裝代理程式的指示。
3. 在 [相依性] 頁面上，下載 Microsoft Monitoring Agent (MMA) 和相依性代理程式，並安裝在屬於群組的每部 VM 上。
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

## <a name="refine-the-group-based-on-dependency-visualization"></a>根據相依性視覺效果調整群組
一旦您已在群組的所有機器上安裝代理程式，您可以將群組的相依性視覺化，並按照下列步驟進行調整。

1. 在 Azure Migrate 專案中的 **管理** 下方，按一下  **群組**，然後選取群組。
2. 在群組頁面中，按一下  **[檢視相依性]**， 即會開啟群組相依性對應。
3. 群組的相依性對應會顯示下列詳細資料：
    - 至/從屬於群組的所有機器之輸入 (用戶端) 和輸出 (伺服器) TCP 連線
        - 未安裝 MMA 和相依性代理程式的相依機器會依連接埠號碼分組
        - 已安裝 MMA 和相依性代理程式的相依機器會以不同的方塊顯示 
    - 在機器內執行的處理序，您可以展開每個機器方塊，以檢視處理序
    - 每部機器的完整網域名稱、作業系統、MAC 位址等屬性，您可以按一下每個機器方塊來檢視這些詳細資料

     ![檢視群組相依性](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. 若要檢視更細微的相依性，請按一下時間範圍以進行修改。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。
4. 確認相依機器、在每部機器內部執行的處理序，並識別應該新增到群組或從中移除的機器。
5. 使用「Ctrl+按一下」選取對應上的多部機器，以將它們新增到群組或從中移除。
    - 您只能新增已探索到的機器。
    - 新增和移除群組中的機器會讓先前所做的評量失效。
    - 修改群組時，可以選擇性地建立新評量。
5. 按一下 [確定] 以儲存群組。

    ![新增或移除機器](./media/how-to-create-group-dependencies/add-remove.png)

如果您要檢查群組相依性對應中顯示之特定機器的相依性，[請設定機器相依性對應](how-to-create-group-machine-dependencies.md)。


## <a name="next-steps"></a>後續步驟

[深入了解](concepts-assessment-calculation.md)評定的計算方式。
