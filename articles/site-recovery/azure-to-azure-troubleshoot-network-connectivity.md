---
title: Azure 對 Azure 網路連線問題和錯誤的 Azure Site Recovery 疑難排解 |Microsoft Docs
description: 針對損毀修復複寫 Azure 虛擬機器時的錯誤和問題進行疑難排解
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 8e1350a22554bab257e8c99954c2beaa357de2ff
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934532"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>針對 Azure 至 Azure VM 網路連線問題進行疑難排解

本文說明當您將 Azure 虛擬機器從一個區域複寫及復原到另一個區域時, 與網路連線相關的常見問題。 如需網路需求的詳細資訊, 請參閱複寫[Azure vm 的連線能力需求](azure-to-azure-about-networking.md)。

若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。

**URL** | **詳細資料**  
--- | ---
*.blob.core.windows.net | 需要此項目方可從 VM 將資料寫入來源地區的快取儲存體帳戶中。 如果您知道 Vm 的所有快取儲存體帳戶, 您可以允許-列出特定的儲存體帳戶 Url (例如, cache1.blob.core.windows.net 和 cache2.blob.core.windows.net), 而不是 *. blob.core.windows.net
login.microsoftonline.com | 需要此項目方可進行 Site Recovery 服務 URL 的授權和驗證。
*.hypervrecoverymanager.windowsazure.com | 需要此項目方可從 VM 進行 Site Recovery 服務通訊。 如果您的防火牆 proxy 支援 Ip, 您可以使用對應的「Site Recovery IP」。
*.servicebus.windows.net | 需要此項目方可從 VM 寫入 Site Recovery 監視和診斷資料。 如果您的防火牆 proxy 支援 Ip, 您可以使用對應的「Site Recovery 監視 IP」。

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題 1：無法向 Site Recovery 註冊 Azure 虛擬機器 (151195) </br>
- **可能的原因** </br>
  - 因為 DNS 解析失敗, 所以無法建立連線至 Site Recovery 端點。
  - 此問題經常會在您已將虛擬機器容錯移轉，但 DR 區域無法連線到 DNS 伺服器的情況下進行重新保護的期間發生。

- **解決方案**
   - 如果您使用的是自訂 DNS, 請確定可從嚴重損壞修復區域存取 DNS 伺服器。 若要查看您是否有自訂 DNS，請移至 VM> [災害復原網路] > [DNS 伺服器]。 嘗試從虛擬機器存取 DNS 伺服器。 如果無法存取, 請將 DNS 伺服器容錯移轉, 或在 DR 網路和 DNS 之間建立這一行網站, 讓它可供存取。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)

> [!NOTE]
> 如果虛擬機器位於**標準**內部負載平衡器後方, 則預設不會有 O365 ip (也就是 login.microsoftonline.com) 的存取權。 請將它變更為 [**基本**內部負載平衡器類型] 或 [建立輸出存取], 如[文章](https://aka.ms/lboutboundrulescli)中所述。

- **可能的原因** </br>
  - 無法建立與 Office 365 驗證與身分識別 IP4 端點之間的連線。

- **解決方案**
  - Azure Site Recovery 需要存取 Office 365 IP 範圍以進行驗證。
    如果您是使用 Azure 網路安全性群組 (NSG) 規則/防火牆 Proxy 來控制 VM 上的輸出網路連線能力，請確保您已允許與 O365 IP 範圍之間的通訊。 建立[Azure Active Directory (Azure AD) 以服務標記](../virtual-network/security-overview.md#service-tags)為基礎的 NSG 規則, 以允許存取對應至 Azure AD 的所有 IP 位址
      - 如果未來將新的位址新增至 Azure AD, 您就必須建立新的 NSG 規則。

### <a name="example-nsg-configuration"></a>範例 NSG 設定

這個範例示範如何針對要複寫的 VM 設定 NSG 規則。

- 如果您使用 NSG 規則來控制輸出連線，請針對所有必要的 IP 位址範圍，將「允許 HTTPS 輸出」規則應用至連接埠：443。
- 此範例假設 VM 來源位置為「美國東部」，而目標位置為「美國中部」。

### <a name="nsg-rules---east-us"></a>NSG 規則：美國東部

1. 在 NSG 上針對 "Storage.EastUS" 建立輸出 HTTPS (443) 安全性規則，如以下螢幕擷取畫面所示。

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 在 NSG 上針對 "AzureActiveDirectory" 建立輸出 HTTPS (443) 安全性規則，如以下螢幕擷取畫面所示。

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. 針對那些對應至目標位置的站台復原 IP，建立輸出 HTTPS (443) 規則：

   **Location** | **Site Recovery 位址** |  **Site Recovery 監視 IP 位址**
    --- | --- | ---
   美國中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 規則：美國中部

需要這些規則，才能啟用從目標區域到來源地區容錯移轉後的複寫：

1. 在 NSG 上針對 "Storage.CentralUS" 建立輸出 HTTPS (443) 安全性規則。

2. 在 NSG 上針對 "AzureActiveDirectory" 建立輸出 HTTPS (443) 安全性規則。

3. 針對那些對應至來源位置的站台復原 IP 建立輸出 HTTPS (443) 規則：

   **Location** | **Site Recovery 位址** |  **Site Recovery 監視 IP 位址**
    --- | --- | ---
   美國中部 | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：Site Recovery 設定失敗 (151197)
- **可能的原因** </br>
  - 無法在連線至 Azure Site Recovery 服務端點。

- **解決方案**
  - 根據區域的不同，Azure Site Recovery 需要存取不同的 [Site Recovery IP 範圍](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。 請確保虛擬機器可存取所需的 IP 範圍。


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題 4：當網路流量通過內部部署 proxy 伺服器時, A2A 複寫失敗 (151072)
- **可能的原因** </br>
  - 自訂 proxy 設定無效, 而且 Azure Site Recovery 行動服務代理程式未自動偵測到來自 IE 的 proxy 設定


- **解決方案**
  1. 行動服務代理程式偵測到 IE 的 Proxy 設定 (在 Windows 上) 和 /etc/environment (在 Linux 上)。
  2. 如果您只想要為 Azure Site Recovery 行動服務設定 proxy, 您可以在 Proxyinfo.conf 中提供 proxy 詳細資料, 其位於:</br>
     - ``/usr/local/InMage/config/`` (在 ***Linux*** 上)
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` (在 ***Windows*** 上)
  3. ProxyInfo.conf 應該要有下列 INI 格式的 Proxy 設定。</br>
                [proxy]</br>
                Address=http://1.2.3.4</br>
                Port=567</br>
  4. Azure Site Recovery 行動服務代理程式僅支援***未經驗證的***proxy。

### <a name="fix-the-problem"></a>修正問題
若要允許[所需的 url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或[所需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), 請依照[網路指引檔](site-recovery-azure-to-azure-networking-guidance.md)中的步驟進行。


## <a name="next-steps"></a>後續步驟
[複寫 Azure 虛擬機器](site-recovery-replicate-azure-to-azure.md)
