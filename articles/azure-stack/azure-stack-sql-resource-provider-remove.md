---
title: 在 Azure Stack 上移除 SQL 資源提供者 | Microsoft Docs
description: 了解如何從 Azure Stack 部署移除 SQL 資源提供者。
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294775"
---
# <a name="removing-the-mysql-resource-provider"></a>移除 MySQL 資源提供者  
在移除 SQL 資源提供者之前，必須先移除任何相依性。

## <a name="remove-the-mysql-resource-provider"></a>移除 MySQL 資源提供者 

1. 請確認您已移除任何現有的 SQL 資源提供者相依性。

  > [!NOTE]
  > 即使相依資源目前正在使用資源提供者，解除安裝 SQL 資源提供者的作業仍會繼續。 
  
2. 確定您具有為此 SQL 資源提供者配接器版本下載的原始部署套件。
3. 使用下列參數來重新執行部署指令碼：
    - 使用 -Uninstall 參數
    - 具特殊權限端點的 IP 位址或 DNS 名稱。
    - 雲端管理員的認證，這是存取具特殊權限端點所需的認證。
    - Azure Stack 服務管理帳戶的認證。 使用部署 Azure Stack 時所使用的相同認證。

## <a name="next-steps"></a>後續步驟
[以 PaaS 的形式提供 App Service](azure-stack-app-service-overview.md)
