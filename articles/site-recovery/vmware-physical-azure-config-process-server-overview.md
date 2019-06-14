---
title: 有關 Azure Site Recovery 設定，處理程序，與主要目標伺服器 |Microsoft Docs
description: 這篇文章提供設定程序及設定使用 Azure Site Recovery 的 Azure 至內部部署 VMware Vm 的災害復原時所使用的主要目標伺服器的概觀
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8b3815fc9dc44484779a70b51ebff4802265d53a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417734"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>關於 Site Recovery 元件 （設定、 處理程序、 主要目標）

這篇文章描述組態、 程序，以及將 VMware Vm 和實體伺服器複寫至 Azure 時所使用的主要目標伺服器[Site Recovery](site-recovery-overview.md)服務。

## <a name="configuration-server"></a>組態伺服器

針對內部部署 VMware Vm 和實體伺服器災害復原，您需要 Site Recovery 組態伺服器部署在內部部署環境。

**設定** | **詳細資料** | **Links**
--- | --- | ---
**元件**  | 組態伺服器機器會執行所有的內部部署 Site Recovery 元件，包括組態伺服器、 處理序伺服器和主要目標伺服器。<br/><br/> 當您設定組態伺服器時，會自動安裝所有元件。 | [讀取](vmware-azure-common-questions.md#configuration-server)configuration server 常見問題集。
**角色** | 組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。 | 深入了解架構[VMware](vmware-azure-architecture.md)並[實體伺服器](physical-azure-architecture.md)災害復原至 Azure。
**VMware 需求** | 針對內部部署 VMware vm 的災害復原，您必須安裝並執行組態伺服器做為內部部署，高可用性的 VMware VM。 | [深入了解](vmware-azure-deploy-configuration-server.md#prerequisites)必要條件。
**VMware 部署** | 我們建議您部署組態伺服器使用已下載的 OVA 範本。 這個方法會提供直接設定符合所有需求和必要條件設定伺服器的方式。<br/><br/> 如果基於某些原因您無法將 VMware VM，使用.OVA 範本部署，您就可以手動，如下所述的實體機器災害復原設定組態伺服器機器。 | [部署](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)使用.OVA 範本。
**實體伺服器需求** | 針對內部部署實體伺服器上的災害復原，您將部署組態伺服器手動。 | [深入了解](physical-azure-set-up-source.md#prerequisites)必要條件。
**實體伺服器部署** | 如果無法安裝做為 VMware VM，您可以在實體伺服器上安裝它。 | [部署](physical-azure-set-up-source.md#set-up-the-source-environment)組態伺服器以手動方式。


## <a name="process-server"></a>處理序伺服器

**設定** | **詳細資料** | **Links**
--- | --- | ---
**部署**  | 對於災害復原和內部部署 VMware Vm 和實體伺服器複寫，您需要的處理序伺服器在內部部署環境。 預設情況下，處理序伺服器會安裝在組態伺服器上，當您在部署時。 | [深入了解](vmware-azure-architecture.md?#architectural-components)。
**角色 （內部部署** | -會從啟用複寫的機器接收複寫資料。<br/> -最佳化與快取、 壓縮和加密複寫資料，並將它傳送至 Azure 儲存體。<br/> -在內部部署 VMware Vm 和您想要複寫的實體伺服器上執行 Site Recovery 行動服務推送的安裝。<br/> -執行自動探索內部部署機器。 | [深入了解](vmware-physical-azure-config-process-server-overview.md#process-server)。 
**角色 （從 Azure 容錯回復）** | 從您的內部部署網站容錯移轉之後, 您設定處理序伺服器，在 Azure 中，為 Azure VM，來處理容錯回復到內部部署位置。<br/><br/> 在 Azure 中的處理序伺服器是暫時性的。 容錯回復完成之後，就可以刪除 Azure VM。 | [深入了解](vmware-azure-set-up-process-server-azure.md)。
**調整大小** | 若是大型部署，內部您可以設定其他的向外延展處理序伺服器。 其他伺服器相應放大的容量，處理更大量的複寫機器，以及更大的磁碟區的複寫流量。<br/><br/> 您可以移動機器之間兩個處理序伺服器，為了達到負載平衡複寫流量。 | [了解更多](vmware-azure-set-up-process-server-scale.md)，


## <a name="master-target-server"></a>主要目標伺服器

主要目標伺服器會在從 Azure 容錯回復期間，處理複寫資料。

- 它會安裝在組態伺服器上的預設值。
- 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。


## <a name="next-steps"></a>後續步驟
- 檢閱[架構](vmware-azure-architecture.md)的 VMware Vm 和實體伺服器災害復原。
- 檢閱[需求和必要條件](vmware-physical-azure-support-matrix.md)VMware Vm 和實體伺服器至 Azure 的災害復原。 
