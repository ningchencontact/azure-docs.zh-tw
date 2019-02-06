---
title: 快速入門 - Azure SQL Database 受控執行個體 | Microsoft Docs
description: 了解如何快速地開始使用 Azure SQL Database - 受控執行個體
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468167"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>開始使用 Azure SQL Database 受控執行個體

[Azure SQL Database 受控執行個體](sql-database-managed-instance-index.yml)為 SQL Server 的完全受控 PaaS 版本，其裝載於 Azure 雲端中，並置於您自己具有私人 IP 位址的 VNet 中。 在本節中，您將了解如何快速地設定並建立 受控執行個體，並移轉您的資料庫。

## <a name="quickstart-overview"></a>快速入門概觀

在本節中，您將能查看可協助您快速開始使用受控執行個體的可用文章概觀。 建立第一個受控執行個體最簡單的方式，便是使用 [Azure 入口網站](sql-database-managed-instance-get-started.md)，其可讓您設定必要參數 (使用者名稱/密碼、核心數目、儲存體上限)，並在無需了解網路詳細資料和基礎結構需求的前提下自動建立 Azure 網路環境。 您只需確保自己具有允許建立該執行個體的[訂用帳戶類型](sql-database-managed-instance-resource-limits.md#supported-subscription-types)。

如果您擁有自己想要使用的網路，或是想要自訂網路，請參閱如何針對受控執行個體[設定網路環境](#configure-network-environment)。

當您建立受控執行個體時，您將會需要使用下列其中一種方式連線到該執行個體：

* 建立 [Azure 虛擬機器](sql-database-managed-instance-configure-vm.md)並搭配安裝 SQL Server Management Studio 和其他應用程式，以用來在與放置受控執行個體相同之 VNet 內的子網路中存取您的受控執行個體。 VM 不能位於與您的受控執行個體相同的子網路。
* 在您的電腦上設定[點對站連線](sql-database-managed-instance-configure-p2s.md)，其可讓您將電腦加入放置受控執行個體的 VNet 中，並以和使用網路中其他 SQL Server 相同的方式使用受控執行個體。

或者，您也可以從區域網路使用 ExpressRoute 或站對站連線，但這些方法並不在這些快速入門所涵蓋的範圍內。

當您建立受控執行個體並設定存取時，您可以開始移轉置於 SQL Server 內部部署或 Azure VM 上的資料庫。 請注意，如果您想要移轉的來源資料庫具有不支援的功能，移轉將會失敗。 若要避免失敗並檢查相容性，您可以安裝 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) \(英文\)，其會分析您位於 SQL Server 上的資料庫，並找出可能會防止移轉到受控執行個體的所有問題 (例如存在 Filestream 或多個記錄檔)。 如果您解決這些問題，您的資料庫便準備好移至受控執行個體。 [資料庫測試助理](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) \(英文\) 是另一個有用的工具，其可記錄您在 SQL Server 上的工作負載，並在受控執行個體上重新播放它，讓您可以判斷在移轉到受控執行個體之後是否會發生任何效能問題。

當您確定自己可以將資料庫移轉到受控執行個體之後，您可以使用[原生 RESTORE](sql-database-managed-instance-get-started-restore.md) 功能來利用 Transact-SQL 命令建立資料庫備份，將它上傳到 Azure Blob 儲存體，然後使用 Transact-SQL 命令從 Blob 儲存體還原資料庫。

這些快速入門可讓您快速地設定及建立資料庫，並將資料庫置於受控執行個體中。 在某些情況下，您必須對受控執行個體和必要網路環境的部署進行自訂或自動化。 這些案例將於下方詳述。

## <a name="customizing-network-environment"></a>自訂網路環境

雖然 VNet/子網路可以在使用 [Azure 入口網站](sql-database-managed-instance-get-started.md)建立執行個體時自動設定，您應該在開始建立受控執行個體之前建立它，以設定 VNet 和子網路的參數。 建立並設定網路環境最簡單的方式，便是使用 [Azure 資源部署](sql-database-managed-instance-create-vnet-subnet.md)範本，其會建立要放置執行個體的網路和子網路。 您只需要按下 Azure Resource Manager 部署按鈕，並將參數填入表單中。 或者，您也可以使用 [PowerShell 指令碼](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) \(英文\) 來將網路的建立自動化。

如果您已經有要部署受控執行個體的 VNet 和子網路，您必須確定您的 VNet 和子網路皆能滿足[網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 您應該使用這個 [PowerShell 指令碼來確認子網路已正確設定](sql-database-managed-instance-configure-vnet-subnet.md)。 此指令碼不只會確認網路並回報問題，它還會告訴您應該變更的項目，並可為您在 VNet/子網路中做出必要的變更。 如果您不想要手動設定 VNet/子網路，請執行此指令碼；此外，當您在未來對網路基礎結構做出任何重大重新設定時，也應該再次執行它。 如果您想要建立並設定自己的網路，請參閱[受控執行個體文件](sql-database-managed-instance-connectivity-architecture.md)和[這篇指南](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01) \(英文\)。

## <a name="automating-creation-of-managed-instance"></a>將受控執行個體的建立自動化

 如果您尚未依照上個步驟中的指示建立網路環境，Azure 入口網站可以代您進行。此作法唯一的缺點在於系統會以預設參數設定網路環境，且您無法於稍後變更這些參數。 作為替代方案，您也可以使用 [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) \(英文\)、[搭配 Resource Manager 範本的 PowerShell](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)，或 [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/) \(英文\)。

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>以最短停機時間移轉到受控執行個體

這些快速入門中的文章可讓您快速地設定受控執行個體並移動您的資料庫。 不過，使用原生還原功能時，您必須等候資料庫還原完畢，這會對您的應用程式造成一段停機時間，特別是在資料庫較大的情況下。 如果您準備移動生產環境資料庫，您應該尋找更好的方法，以在移轉時保證最短的停機時間。 [資料移轉服務](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)是能以最短停機時間移轉您資料庫的移轉服務，其能將在來源資料庫中所做的變更，以累加方式推送到您正在還原到受控執行個體的資料庫中。 透過此方法，您可以在最短停機時間的前提下，快速地將應用程式從來源資料庫切換到目標資料庫。

## <a name="next-steps"></a>後續步驟

* 在[這裡取得受控執行個體中已支援功能的高階清單](sql-database-features.md)，並在[這裡取得詳細資料和已知問題](sql-database-managed-instance-transact-sql-information.md)。 
* 了解[受控執行個體的技術特性](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。 
* 在[操作指南小節中](sql-database-howto-managed-instance.md)尋找更為進階的教學課程。 
