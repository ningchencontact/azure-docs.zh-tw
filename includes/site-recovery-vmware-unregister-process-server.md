---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170002"
---
請遵循您的特定情況下的步驟。

### <a name="unregister-a-connected-process-server"></a>取消註冊已連線的處理序伺服器

1. 建立系統管理員身分的處理序伺服器的遠端連線。
2. 在 **控制台中**，開啟**程式 > 解除安裝程式**。
3. 解除安裝程式**Microsoft Azure Site Recovery Mobility Service/主要目標伺服器**。
4. 解除安裝程式**Microsoft Azure Site Recovery Configuration/Process Server**。
5. 步驟 3 和 4 中的程式會解除安裝之後，解除安裝**Microsoft Azure Site Recovery 組態/處理序伺服器相依性**。

### <a name="unregister-a-disconnected-process-server"></a>取消註冊已中斷連線的處理序伺服器

如果沒有沒辦法恢復處理序伺服器安裝所在的機器，請只使用這些步驟。

1. 系統管理員身分登入組態伺服器。
2. 開啟 系統管理命令提示字元，並瀏覽至`%ProgramData%\ASR\home\svsystems\bin`。
3. 執行下列命令可取得一或多個處理序伺服器的清單。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. 否： 處理序伺服器的數列數字。
    - IP/名稱：IP 位址名稱與執行處理序伺服器的電腦。
    - 活動訊號：從處理序伺服器電腦的上次活動訊號。
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 指定您想要取消註冊處理序伺服器的序號。
5. 取消註冊處理序伺服器會從系統移除所有其詳細資料，並顯示訊息：**已成功取消註冊伺服器名稱 > （伺服器 IP 位址）**

