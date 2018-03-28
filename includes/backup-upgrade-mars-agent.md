---
title: 升級 Azure 備份代理程式
description: 這項資訊會說明您應升級 Azure 備份代理程式的原因，以及可從何處下載升級。
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
## <a name="upgrade-the-mars-agent"></a>升級 MARS 代理程式
2.0.9083.0 以下的 Microsoft Azure 復原服務 (MARS) 代理程式版本會與 Azure 存取控制服務 (ACS) 相依。 在 2018 年時，Azure 會[取代 Azure 存取控制服務 (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). 從 2018 年 3 月 19 日開始，2.0.9083.0 以下的所有 MARS 代理程式版本會發生備份失敗。 若要避免或解決備份失敗，請[將 MARS 代理程式升級至最新版本](https://go.microsoft.com/fwlink/?linkid=229525)。 若要識別需要升級 MARS 代理程式的伺服器，請遵循[備份部落格中的步驟來升級 Azure 備份代理程式](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)。 MARS 代理程式可用來將下列資料類型備份至 Azure：
- 檔案 
- 系統狀態資料
- 將 System Center DPM 備份至 Azure
- Azure 備份伺服器 (MABS)
