---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908530"
---
取消註冊處理序伺服器的步驟，隨著其與設定伺服器的連線狀態而有所不同。

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>取消註冊處於已連線狀態的處理序伺服器

1. 以系統管理員身分遠端登入處理序伺服器。
2. 啟動**控制台中**，然後開啟**程式 > 解除安裝程式**。
3. 解除安裝程式，名為**Microsoft Azure Site Recovery Mobility Service/主要目標伺服器**。
4. 解除安裝程式，名為**Microsoft Azure Site Recovery Configuration/Process Server**。
5. 步驟 3 和 4 中的程式會解除安裝時，您可以解除安裝**Microsoft Azure Site Recovery 組態/處理序伺服器相依性**。

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>取消註冊處於已中斷連線狀態的處理序伺服器

> [!WARNING]
> 使用下列步驟，如果沒有沒辦法恢復處理序伺服器已安裝所在的虛擬機器。

1. 以系統管理員身分登入組態伺服器。
2. 開啟系統管理命令提示字元並瀏覽至目錄 `%ProgramData%\ASR\home\svsystems\bin`。
3. 現在執行命令。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 上述命令會提供處理序伺服器 (有重複項目時，可能不只一個) 清單，以及序號 (S.No)、IP 位址 (IP) 和處理序伺服器部署所在 VM 的名稱 (Name) 和 VM 的活動訊號 (Heartbeat)，如下所示。
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. 現在，輸入您想要取消註冊的處理序伺服器序號。
6. 這會從系統清除處理序伺服器的詳細資料，並會顯示訊息：**已成功取消註冊伺服器名稱 > （伺服器 IP 位址）**

