---
title: "針對在從 Azure 容錯回復至內部部署且之後重新保護至 Azure 期間發生的失敗進行疑難排解 | Microsoft Docs"
description: "本文所說明的方式可針對在從 Azure 容錯回復到內部部署和重新保護期間發生的常見錯誤進行疑難排解"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>針對容錯回復程序期間所回報的錯誤進行疑難排解
容錯回復基本上涉及兩個主要步驟。 一個是重新保護虛擬機器從 Azure 至內部部署，第二個則是實際執行從 Azure 容錯回復至內部部署。

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>針對在容錯移轉之後，重新保護虛擬機器回到內部部署時所發生的錯誤進行疑難排解
執行重新保護虛擬機器至 Azure 時，您可能會收到下列其中一個錯誤。 若要進行疑難排解，請使用針對每種錯誤狀況所述的步驟。


### <a name="error-code-95226"></a>錯誤碼 95226

重新保護失敗，因為 Azure 虛擬機器無法觸達內部部署組態伺服器。

發生這種情況的時機： 
1. Azure 虛擬機器無法觸達內部部署組態伺服器，因此無法加以探索和註冊到組態伺服器。 
2. Azure 虛擬機器上必須執行才能與內部部署組態伺服器通訊的 InMage Scout 應用程式服務，在容錯移轉後可能不會執行。

若要解決此問題
1. 您必須確定已設定 Azure 虛擬機器的網路，使虛擬機器可與內部部署組態伺服器進行通訊。 若要這樣做，請將網站對網站 VPN 設定回到您的內部部署資料中心，或是使用 Azure 虛擬機器之虛擬網路上的私人對等互連來設定 ExpressRoute 連線。 
2. 如果您已將網路設定為 Azure 虛擬機器可與內部部署組態伺服器進行通訊，請登入虛擬機器並檢查 'InMage Scout Application Service'。 如果您觀察到 InMage Scout 應用程式服務未執行，則以手動方式啟動服務，並確定已將服務啟動類型設為自動。

### <a name="error-code-78052"></a>錯誤碼 78052
重新保護會失敗，並出現錯誤訊息：*無法完成虛擬機器的保護。*

可能有兩個原因會造成這個問題
1. 您要重新保護的虛擬機器是 Windows Server 2016。 此作業系統目前不支援容錯回復，但很快就會支援。
2. 在您要容錯回復的對象主要目標伺服器上，已經存在具有相同名稱的虛擬機器。

若要解決這個問題，您可以在不同的主機上選取不同的主要目標伺服器，使重新保護可在不同的主機上建立機器，而其中的名稱不會衝突。 您也可以將主要目標 vMotion 到不同的主機，而其中的名稱不會發生衝突。 如果現有的虛擬機器為偏離機器，您可以重新命名它，以在相同的 ESXi 主機上建立新的虛擬機器。

### <a name="error-code-78093"></a>錯誤碼 78093

VM 未執行，處於停止回應狀態或無法存取。

若要重新保護容錯移轉的虛擬機器回到內部部署，Azure 虛擬機器必須為執行中。 如此一來，行動服務就會向組態伺服器內部部署進行註冊，並可與流程伺服器通訊，開始進行複寫。 如果電腦在不正確的網路上或未執行 (停止回應狀態或關機)，組態伺服器就無法觸達虛擬機器中的行動服務以開始重新保護。 您可以重新啟動虛擬機器，讓它能夠開始通訊回內部部署。 啟動 Azure 虛擬機器之後，重新啟動重新保護作業

### <a name="error-code-8061"></a>錯誤碼 8061

*無法從 ESXi 主機存取資料存放區。*

請參閱[主要目標必要條件](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)和[支援資料存放區](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)以進行容錯回復


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>針對在執行將 Azure 虛擬機器容錯回復回到內部部署時所發生的錯誤進行疑難排解
執行將 Azure 虛擬機器容錯回復回到內部部署時，您可能會收到下列其中一個錯誤。 若要進行疑難排解，請使用針對每種錯誤狀況所述的步驟。

### <a name="error-code-8038"></a>錯誤碼 8038

*由於發生錯誤而無法啟動內部部署的虛擬機器*

這可能會在未佈建足夠記憶體的主機上啟動內部部署虛擬機器時發生。

若要解決此問題

1. 您可以在 ESXi 主機上佈建更多記憶體。
1. 將虛擬機器 vMotion 到另一台有足夠記憶體可啟動虛擬機器的 ESXi 主機。