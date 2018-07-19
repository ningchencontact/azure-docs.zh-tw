---
title: Azure 基礎結構完整性
description: 本文說明 Azure 基礎結構的完整性。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901311"
---
# <a name="azure-infrastructure-integrity"></a>Azure 基礎結構完整性

## <a name="software-installation"></a>軟體安裝
在軟體堆疊中，安裝在 Azure 環境下的所有元件都是遵循 Microsoft 安全性開發生命週期 (SDL) 程序以自訂方式建置的。 所有軟體元件 (包括 OS 映像和 SQL Database) 都會在變更和發行管理程序中部署。 在所有節點上執行的 OS 都是 Windows Server 2008 或 Windows Server 2012 的自訂版本。 FC 會根據希望 OS 扮演的角色，選擇確切的版本。 此外，主機 OS 不允許安裝任何未經授權的軟體元件。

有些 Microsoft Azure 元件 (例如 RDFE、開發人員入口網站等) 會當作 Azure 客戶，部署在於客體 OS 上執行的客體 VM 中。

## <a name="virus-scans-on-builds"></a>針對組建進行病毒掃描
Azure 軟體元件 (包括 OS) 組建必須使用 Microsoft Endpoint Protection (MEP) 防毒工具進行病毒掃描。 每次執行病毒掃描都會在相關聯的組建目錄內建立記錄檔，詳述掃描的項目和掃描的結果。 病毒掃描是 Azure 中每個元件組建原始程式碼的一部分。 如果沒有進行乾淨且成功的病毒掃描，程式碼就不會移至生產階段。 如果發生任何問題，組建就會被凍結，然後將會移至 Microsoft Security 內的安全性小組，找出「不良的」程式碼進入組建的位置。

## <a name="closedlocked-environment"></a>封閉/鎖定的環境
根據預設，Azure 基礎結構節點和客體 VM 沒有在其上建立任何使用者帳戶。 此外，預設 Windows 系統管理員帳戶也會被停用。 Microsoft Azure Live Support (WALS) 的系統管理員可以使用適當的驗證，登入這些電腦並管理 Azure 生產網路以進行緊急修復。

## <a name="microsoft-azure-sql-database-authentication"></a>Microsoft Azure SQL Database 驗證
如同 SQL Server 的任何實作，使用者帳戶管理必須受到嚴格控管。 Microsoft Azure SQL Database 只支援 SQL Server 驗證。 具有強式密碼並以特定權限設定的使用者帳戶也應該用來補充客戶的資料安全性模型。

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>MSFT 公司網路與 Microsoft Azure 叢集之間的防火牆/ACL
服務平台和 MS 公司網路之間的 ACL/防火牆會防止未經授權的測試人員存取 Microsoft Azure SQL Database。 此外，只有來自 Microsoft 公司網路 IP 位址範圍的使用者可以存取 WinFabric 平台管理端點。

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Azure SQL DB 叢集中節點之間的防火牆/ACL
在 Microsoft Azure SQL DB 叢集中的節點之間已實作 ACL/防火牆，作為深度防禦策略的一部分，以提額外的保護。 WinFabric 平台叢集內的所有通訊以及所有執行中的程式碼都會受到信任。

## <a name="custom-mas-watchdogs"></a>自訂 MA (監視程式)
Microsoft Azure SQL Database 運用自訂 MA (稱為監視程式) 來監視 Microsoft Azure SQL DB 叢集的健康情況。

## <a name="web-protocols"></a>Web 通訊協定

### <a name="role-instance-monitoring-and-restart"></a>角色執行個體監視與重新啟動
Azure 可確保所有部署的執行中角色 (網際網路面向 Web 或後端處理背景工作角色) 受到持續的健康情況監視，以確保這些角色有效且有效率地提供佈建這些角色所在的服務。 萬一因為所裝載的應用程式發生重大錯誤，或在角色執行個體本身發生底層設定問題而使角色變成狀況不良，Microsoft Azure FC 將偵測角色執行個體內的問題，並起始更正狀態。

### <a name="compute-connectivity"></a>計算連線能力
Azure 可確保能夠透過標準的 Web 通訊協定與所部署的應用程式/服務連線。 網際網路面向 Web 角色虛擬執行個體將具備外部網際網路連線能力，因此可供 Web 使用者直接存取。 後端處理背景工作角色虛擬執行個體具備外部網際網路連線能力，但無法由外部 Web 使用者直接存取，以保護背景工作角色代表可公開存取 Web 角色虛擬執行個體所執行作業的機密性和完整性。

## <a name="next-steps"></a>後續步驟
若要深入了解 Microsoft 為保護 Azure 基礎結構執行了哪些動作，請參閱：

- [Azure 設備、廠房以及實體安全性](azure-physical-security.md)
- [Azure 基礎結構可用性](azure-infrastructure-availability.md)
- [Azure 資訊系統元件和界限](azure-infrastructure-components.md)
- [Azure 網路架構](azure-infrastructure-network.md)
- [Azure 生產網路](azure-production-network.md)
- [Microsoft Azure SQL Database 安全性功能](azure-infrastructure-sql.md)
- [Azure 生產作業和管理](azure-infrastructure-operations.md)
- [監視 Azure 基礎結構](azure-infrastructure-monitoring.md)
- [在 Azure 中保護客戶資料](azure-protection-of-customer-data.md)
