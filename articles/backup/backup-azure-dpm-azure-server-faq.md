---
title: Azure 備份伺服器和 DPM 常見問題集
description: 有關以下常見問題的解答：Azure 備份伺服器和 DPM。
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 7a598038ee435b67b9ad8f06bdec2490bc1c53c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705109"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure 備份伺服器和 DPM-常見問題集
本文會回答有關 Azure 備份伺服器和 DPM 常見問題的解答。

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>可以使用 Azure 備份伺服器來為實體伺服器建立裸機復原 (BMR) 備份嗎？ <br/>
是的。

### <a name="can-i-register-the-server-to-multiple-vaults"></a>是否可以向多個保存庫註冊伺服器？
資料分割 只能向一個保存庫註冊 DPM 或「Azure 備份」伺服器。


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>可以使用 DPM 備份 Azure Stack 中的應用程式嗎？
資料分割 雖然您可以使用 Azure 備份來保護 Azure Stack，但 Azure 備份不支援使用 DPM 來備份 Azure Stack 中的應用程式。

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>如果我已安裝「Azure 備份」代理程式來保護檔案和資料夾，是否可以安裝 System Center DPM 將內部部署工作負載備份至 Azure？
是的。 但您應該先安裝 DPM，然後再安裝「Azure 備份」代理程式。  依此順序安裝元件可確保「Azure 備份」代理程式能與 DPM 搭配運作。 不建議或不支援在安裝 DPM 之前先安裝代理程式。

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>為什麼不能在安裝 UR7 和最新的 Azure 備份代理程式之後，從另一部 DPM 伺服器新增外部 DPM 伺服器？
針對資料來源已在雲端受保護 (藉由使用比「更新彙總套件 7」舊的更新彙總套件進行保護) 的 DPM 伺服器，您必須在安裝 UR7 和最新的 Azure 備份代理程式之後至少等候一天，再開始「新增外部 DPM 伺服器」  。 需要一天的時間才能將 DPM 保護群組的中繼資料上傳至 Azure。 保護群組中繼資料第一次會透過夜間作業上傳。

## <a name="vmware-and-hyper-v-backup"></a>VMware 和 Hyper-V 備份

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>我可以將 VMware vCenter 伺服器備份到 Azure 嗎？
是的。 您可以使用「Azure 備份伺服器」將 VMware vCenter Server 和 ESXi 主機備份至 Azure。

- [深入了解](backup-mabs-protection-matrix.md)支援的版本。
- [依照這些步驟](backup-azure-backup-server-vmware.md)來備份 VMware 伺服器。

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>我是否需要完全在內部部署 VMware/HYPER-V 叢集復原個別的授權？
您不需要針對 VMware/HYPER-V 保護進行個別授權。

- 如果您是 System Center 客戶，請使用 System Center Data Protection Manager (DPM) 來保護 VMware VM。
- 如果您不是 System Center 客戶，您可使用 Azure 備份伺服器 (隨用隨付) 來保護 VMware VM。


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>如果使用 SQL AlwaysOn (使用磁碟上保護) 設定 SharePoint，我是否能將 SharePoint 項目復原到原始位置？
可以，項目可以復原到原始的 SharePoint 網站。

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>如果使用 SQL AlwaysOn 設定 SharePoint，我是否能將 SharePoint 資料庫復原到原始位置？
由於 SharePoint 資料庫是在 SQL AlwaysOn 中設定，所以除非移除可用性群組，否則無法修改它們。 因此，DPM 無法將資料庫還原到原始位置。 您可以將 SQL Server 資料庫復原到其他 SQL Server 執行個體。

## <a name="next-steps"></a>後續步驟

閱讀其他常見問題集：

- [了解更多](backup-support-matrix-mabs-dpm.md)關於 Azure 備份伺服器和 DPM 支援矩陣。
- [了解更多](backup-azure-mabs-troubleshoot.md)關於 Azure 備份伺服器和 DPM 疑難排解指導方針。
