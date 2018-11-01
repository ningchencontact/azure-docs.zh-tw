---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165497"
---
* 使用遠端桌面連線來連接到處理序伺服器虛擬機器。
* 您可以按一下桌面上的可用捷徑來啟動 cspsconfigtool.exe (如果這是您第一次登入該處理伺服器，將會自動啟動此工具)。
  - 設定伺服器的完整名稱 (FQDN) 或 IP 位址
  - 設定伺服器用於接聽的連接埠。 此值應該是 443。
  - 用於連接到設定伺服器的連接複雜密碼。
  - 要針對此處理序伺服器設定的資料傳輸連接埠。 保留預設值，除非您已將它變更為您環境中的其他連接埠號碼。

    ![註冊處理序伺服器](./media/site-recovery-vmware-register-process-server/register-ps.png)
* 按一下 [儲存] 按鈕以儲存設定，並註冊處理序伺服器。
