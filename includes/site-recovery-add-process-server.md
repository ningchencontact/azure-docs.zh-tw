---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: c9a0d4387511bbfa033bcb90d9f83e1a7bb39719
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113993"
---
1. 啟動 Azure Site Recovery UnifiedSetup.exe
2. 在“准备阶段”中选择“添加额外的进程服务器以扩大部署”。

   ![新增處理序伺服器](./media/site-recovery-add-process-server/ps-page-1.png)

3. 在 [組態伺服器詳細資料] 中，指定組態伺服器的 IP 位址，以及複雜密碼。

   ![新增處理序伺服器 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. 在 [網際網路設定] 中，指定在組態伺服器上執行的 Provider 要如何透過網際網路連接到 Azure Site Recovery。

   ![新增處理序伺服器 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * 如果希望使用当前已在计算机上设置的代理进行连接，请选择“使用现有代理设置进行连接” 。
   * 如果您想要讓 Provider 直接連線，請選取 [不使用 Proxy 直接連線]。
   * 如果現有的 Proxy 需要驗證，或是您想要讓 Provider 使用自訂 Proxy 來連線，請選取 [以自訂 Proxy 設定連線]。

     * 若使用自定义代理，需指定地址、端口和凭据。
     * 如果您使用 Proxy，您應該已經獲准存取服務 URL。

5. 在 [必要條件檢查] 中，安裝程式會執行檢查來確定可以執行安裝。 如果看到有关全局时间同步检查的警告，请检查系统时钟的时间（“日期和时间”设置）是否与时区相同。

     ![新增處理序伺服器 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. 在 [環境詳細資料] 中，選取您是否要複寫 VMware VM。 如果是的話，安裝程式會檢查是否已安裝 PowerCLI 6.0。

     ![新增處理序伺服器 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. 在 [安裝位置] 中，選取您要安裝二進位檔及儲存快取的位置。 您選取的磁碟機至少必須有 5 GB 的可用磁碟空間，但我們建議快取磁碟機至少有 600 GB 的可用空間。
     ![新增處理序伺服器 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. 在 [網路選取] 中，指定組態伺服器用來傳送和接收複寫資料的接聽程式 (網路介面卡和 SSL 連接埠)。 連接埠 9443 是用來傳送及接收複寫流量的預設連接埠，但您可以修改此連接埠號碼，以符合您的環境需求。 除了連接埠 9443 之外，我們也會開啟網頁伺服器用來協調複寫作業的連接埠 443。 請勿使用連接埠 443 來傳送或接收複寫流量。

     ![新增處理序伺服器 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. 在“摘要”中复查信息，然后单击“安装”。 安装完成后，将生成密码。 在您啟用複寫時會需要它，所以請將它複製並保存在安全的位置。

     ![新增處理序伺服器 7](./media/site-recovery-add-process-server/ps-page-8.png)
