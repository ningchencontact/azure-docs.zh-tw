---
title: 使用 Azure 角色型存取控制管理備份
description: 使用角色型存取控制來管理復原服務保存庫中的備份管理作業存取權。
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: f293f642db2bd526e761ff570ce97a33845808b7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412800"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>使用角色型存取控制來管理 Azure 備份復原點
Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。

> [!IMPORTANT]
> Azure 備份所提供的角色僅限執行可在 Azure 入口網站或復原服務保存庫 PowerShell Cmdlet 中執行的動作。 在 Azure 備份代理程式用戶端 UI、System Center Data Protection Manager UI 或 Azure 備份伺服器 UI 中執行的動作則非這些角色所能控制。

Azure 備份提供 3 種用來控制備份管理作業的內建角色。 深入了解 [Azure RBAC 內建角色](../role-based-access-control/built-in-roles.md)

* [備份參與者](../role-based-access-control/built-in-roles.md#backup-contributor) - 此角色具有所有用來建立和管理備份的權限，但用來建立復原服務保存庫和賦予他人存取權的權限除外。 您可以將此角色想做是管理備份的系統管理員，其可執行每一種備份管理作業。
* [備份操作員](../role-based-access-control/built-in-roles.md#backup-operator) - 此角色擁有參與者的所有權限，但用來移除備份和管理備份原則的權限除外。 此角色相當於參與者，但無法執行破壞性作業，例如停止備份並刪除資料，或移除內部部署資源的註冊。
* [備份讀取者](../role-based-access-control/built-in-roles.md#backup-reader) - 此角色擁有用來檢視所有備份管理作業的權限。 您可以將此角色想做是監視者。

如果您想要定義自己的角色，獲得更進一步控制，請參閱如何建立 [Azure RBAC 中的自訂角色](../role-based-access-control/custom-roles.md)。



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>將備份的內建角色對應至備份管理動作
下表會擷取備份管理動作與執行該作業所需的最小對應 RBAC 角色。

| 管理作業 | 所需的最小 RBAC 角色 | 所需的範圍 |
| --- | --- | --- |
| 建立復原服務保存庫 | 參與者 | 包含保存庫的資源群組 |
| 啟用 Azure VM 的備份 | 備份操作員 | 包含保存庫的資源群組 |
| | 虛擬機器參與者 | VM 資源 |
| VM 的隨選備份 | 備份操作員 | 復原保存庫資源 |
| 還原 VM | 備份操作員 | 將部署 VM 的資源群組 |
| | 虛擬機器參與者 | 將部署 VM 的資源群組 |
| 還原非受控磁碟 VM 備份 | 備份操作員 | 復原保存庫資源 |
| | 虛擬機器參與者 | VM 資源 |
| | 儲存體帳戶參與者 | 儲存體帳戶資源 |
| 從 VM 備份還原受控磁碟 | 備份操作員 | 復原保存庫資源 |
| | 虛擬機器參與者 | VM 資源 |
| | 儲存體帳戶參與者 | 儲存體帳戶資源 |
| | 參與者 | 要將受控磁碟還原到其中的資源群組 |
| 從 VM 備份還原個別檔案 | 備份操作員 | 復原保存庫資源 |
| | 虛擬機器參與者 | VM 資源 |
| 建立 Azure VM 備份的備份原則 | 備份參與者 | 復原保存庫資源 |
| 修改 Azure VM 備份的備份原則 | 備份參與者 | 復原保存庫資源 |
| 刪除 Azure VM 備份的備份原則 | 備份參與者 | 復原保存庫資源 |
| 在 VM 備份上停止備份 (保留資料或刪除資料) | 備份參與者 | 復原保存庫資源 |
| 註冊內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份操作員 | 復原保存庫資源 |
| 刪除已註冊的內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份參與者 | 復原保存庫資源 |

## <a name="next-steps"></a>後續步驟
* [角色型存取控制](../role-based-access-control/role-assignments-portal.md)：開始在 Azure 入口網站中使用 RBAC。
* 了解如何使用下列各項管理存取權：
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [角色型存取控制疑難排解](../role-based-access-control/troubleshooting.md)︰取得修正常見問題的建議。
