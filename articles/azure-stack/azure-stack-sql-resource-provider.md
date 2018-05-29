---
title: 在 Azure Stack 上使用 SQL 資料庫 | Microsoft Docs
description: 了解如何在 Azure Stack 上部署 SQL 資料庫即服務，並了解部署 SQL Server 資源提供者配接器的快速步驟。
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206156"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 SQL 資料庫
使用 SQL Server 資源提供者配接器，將 SQL 資料庫公開為 [Azure Stack](azure-stack-poc.md) 的服務。 安裝資源提供者並將它連接至一或多個 SQL Server 執行個體之後，您和您的使用者可以建立：
- 適用於雲端原生應用程式的資料庫。
- 以 SQL 為基礎的網站。
- 以 SQL 為基礎的工作負載。
您無須每次佈建裝載 SQL Server 的虛擬機器 (VM)。

資源提供者並未支援 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 的所有資料庫管理功能。 例如，不提供彈性資料庫集區及自動提升和降低資料庫效能的功能。 但是，資源提供者支援類似的建立、讀取、更新及刪除 (CRUD) 作業。 API 與 SQL Database 不相容。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 資源提供者配接器架構
資源提供者是由三個元件所組成：

- **SQL 資源提供者配接器 VM**這是執行提供者服務的 Windows 虛擬機器。
- **資源提供者本身**，它會處理佈建要求並公開資料庫資源。
- **裝載 SQL Server 的伺服器**：為稱為主控伺服器的資料庫提供容量。

您必須建立一個 (或多個) SQL Server 執行處理和/或提供對外部 SQL Server 執行個體的存取權。

> [!NOTE]
> 建立安裝在 Azure Stack 整合式系統上的主控伺服器時，必須從租用戶訂用帳戶建立。 無法從預設提供者訂用帳戶建立這些伺服器。 必須從租用戶入口網站或從具有適當登入的 PowerShell 工作階段來建立這些伺服器。 所有主控伺服器都是可計費的 VM，而且必須具有適當的授權。 服務管理員可以是租用戶訂用帳戶的擁有者。


## <a name="next-steps"></a>後續步驟

[部署 SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)
