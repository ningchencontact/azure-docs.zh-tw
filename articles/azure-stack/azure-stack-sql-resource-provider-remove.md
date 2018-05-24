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
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206155"
---
# <a name="remove-the-sql-resource-provider"></a>移除 SQL 資源提供者

若要移除 SQL 資源提供者，必須先移除任何相依性：

1. 確定您具有為此 SQL 資源提供者配接器版本下載的原始部署套件。

2. 必須從資源提供者刪除所有使用者資料庫。 (刪除使用者資料庫並不會刪除資料)。此工作應該由使用者自己執行。

3. 系統管理員必須從 SQL 資源提供者配接器刪除主控伺服器。

4. 系統管理員必須刪除參考 SQL 資源提供者配接器的所有方案。

5. 系統管理員必須刪除與 SQL 資源提供者配接器關聯的所有 SKU 和配額。

6. 使用下列元素來重新執行部署指令碼：
    - -Uninstall 參數
    - Azure Resource Manager 端點
    - DirectoryTenantID
    - 服務管理員帳戶的認證

