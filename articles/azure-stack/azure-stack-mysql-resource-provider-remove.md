---
title: 在 Azure Stack 上移除 MySQL 資源提供者 | Microsoft Docs
description: 了解如何從 Azure Stack 部署移除 MySQL 資源提供者。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: dcd1c40717cb35fe4daa9ab9e2c66f334ffff5fe
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361493"
---
# <a name="remove-the-mysql-resource-provider"></a>移除 MySQL 資源提供者

在您移除 MySQL 資源提供者之前，必須先移除所有提供者相依性。 您還需要一份用來安裝資源提供者的部署套件複本。

## <a name="dependency-cleanup"></a>相依性清除

在您執行 DeployMySqlProvider.ps1 指令碼來移除資源提供者之前，需要執行幾項清除工作。

租用戶負責執行下列清除工作：

* 從資源提供者刪除其所有資料庫。 (刪除租用戶資料庫並不會刪除資料)。
* 取消註冊提供者命名空間。

系統管理員負責執行下列清除工作：

* 從 MySQL 介面卡刪除主控伺服器。
* 刪除任何參照 MySQL 介面卡的方案。
* 刪除任何與 MySQL 配接器相關聯的配額。

## <a name="to-remove-the-mysql-resource-provider"></a>移除 MySQL 資源提供者

1. 確認您已移除所有現有的 MySQL 資源提供者相依性。

   >[!NOTE]
   >即使相依資源目前正在使用資源提供者，解除安裝 MySQL 資源提供者的作業仍會繼續。
  
2. 取得一份 MySQL 資源提供者複本，然後執行自我解壓縮程式，以將內容解壓縮至暫存目錄。
3. 取得一份 SQL 資源提供者複本，然後執行自我解壓縮程式，以將內容解壓縮至暫存目錄。
4. 開啟新的已提升權限 PowerShell 主控台視窗，然後變更至您解壓縮 MySQL 資源提供者二進位檔的目錄。
5. 使用下列參數來執行 DeployMySqlProvider.ps1 指令碼：
    - **Uninstall**。 移除資源提供者及所有關聯的資源。
    - **PrivilegedEndpoint**。 具特殊權限端點的 IP 位址或 DNS 名稱。
    - **AzureEnvironment**。 用來部署 Azure Stack 的 Azure 環境。 只有部署 Azure AD 時才需要。
    - **CloudAdminCredential**。 雲端管理員的認證，這是存取具特殊權限端點所需的認證。
    - **DirectoryTenantID**
    - **AzCredential**。 Azure Stack 服務管理帳戶的認證。 使用與部署 Azure Stack 時所用認證相同的認證。

## <a name="next-steps"></a>後續步驟

[以 PaaS 的形式提供 App Service](azure-stack-app-service-overview.md)
