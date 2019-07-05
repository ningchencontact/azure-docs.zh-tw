---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534585"
---
1. 建立遠端桌面連線到執行處理序伺服器的機器。 
2. 執行 cspsconfigtool.exe 以啟動 Azure Site Recovery Process Server 組態工具。
    - 此工具會自動啟動第一次登入處理序伺服器。
    - 如果未自動開啟，請按一下其桌面上的捷徑。

3. 在 **組態伺服器 FQDN 或 IP**，指定的名稱或 IP 位址，用來註冊處理序伺服器的組態伺服器。
4. 在 **組態伺服器的連接埠**，請確定已指定 443。 這是針對要求設定伺服器接聽的連接埠。
5. 在 **連線複雜密碼**，指定您指定當您設定組態伺服器複雜密碼。 若要尋找的複雜密碼：
    -  在組態伺服器上，瀏覽至 Site Recovery 安裝資料夾 * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - 執行下列命令，印出目前的複雜密碼：
    ```
    genpassphrase.exe -n
    ```

6. 在 **資料傳輸通訊埠**，保留預設值，除非您指定自訂連接埠。

7. 按一下 **儲存**儲存設定，並註冊處理序伺服器。

    
    ![註冊處理序伺服器](./media/site-recovery-vmware-register-process-server/register-ps.png)
