---
title: 支援使用 Azure Site Recovery 搭配 Azure 備份 |Microsoft Docs
description: 概述如何 Azure Site Recovery 和 Azure 備份可以搭配使用。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035746"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>使用 Site Recovery 搭配 Azure 備份支援

本文摘要說明使用的支援[Site Recovery 服務](site-recovery-overview.md)連同[Azure 備份服務](https://docs.microsoft.com/azure/backup/backup-overview)。

**Action** | **Site Recovery 支援** | **詳細資料**
--- | --- | ---
**將服務部署在一起** | 支援 | 服務互通性，可以一起設定。
**檔案備份/還原** | 支援 | 當備份和複寫啟用 VM 的備份，在還原來源端的 Vm 或一組 Vm 上的檔案會沒有問題。 複寫會如往常般繼續並沒有變更的複寫健康情況。
**磁碟備份/還原** | 沒有目前的支援 | 如果您要還原備份的磁碟，您需要停用並重新啟用 VM 的複寫一次。
**VM 備份/還原** | 沒有目前的支援 | 如果您備份或還原的 VM 或 Vm 群組，您需要停用並重新啟用 VM 的複寫。  


