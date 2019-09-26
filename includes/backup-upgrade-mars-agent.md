---
title: 升級 Azure 備份代理程式
description: 這項資訊會說明您應升級 Azure 備份代理程式的原因，以及可從何處下載升級。
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251533"
---
## <a name="upgrade-the-mars-agent"></a>升級 MARS 代理程式

2\.0.9083.0 以下的 Microsoft Azure 復原服務 (MARS) 代理程式版本會與 Azure 存取控制服務 (ACS) 相依。 MARS 代理程式也稱為 Azure 備份代理程式。 在 2018 年時，Azure 已[淘汰 Azure 存取控制服務 (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md)。 從 2018 年 3 月 19 日開始，2.0.9083.0 以下的所有 MARS 代理程式版本會發生備份失敗。 若要避免或解決備份失敗，請[將 MARS 代理程式升級至最新版本](https://go.microsoft.com/fwlink/?linkid=229525)。 若要識別需要升級 MARS 代理程式的伺服器，請遵循[備份部落格中的步驟來升級 MARS 代理程式](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)。 MARS 代理程式可用來將檔案和資料夾以及系統狀態資料備份至 Azure。 System Center DPM 和 Azure 備份伺服器使用 MARS 代理程式來將資料備份至 Azure。
