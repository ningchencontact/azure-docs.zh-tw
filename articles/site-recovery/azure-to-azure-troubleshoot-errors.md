---
title: Azure 至 Azure 複寫問題和錯誤的 Azure Site Recovery 疑難排解 | Microsoft Docs
description: 對於複寫 Azure 虛擬機器進行嚴重損壞修復時發生的錯誤和問題進行疑難排解
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sujayt
ms.openlocfilehash: 62a2da72a2659b95e4da41de67da4c609b8f049e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835577"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure 至 Azure VM 複寫問題的疑難排解

本文說明從一個區域將 Azure 虛擬機器複寫和復原到另一個區域時，關於 Azure Site Recovery 的常見問題，並解說如何進行疑難排解。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="list-of-errors"></a>錯誤清單
- **[Azure 資源配額問題 (錯誤碼 150097)](#azure-resource-quota-issues-error-code-150097)** 
- **[受信任的根憑證 (錯誤碼 151066)](#trusted-root-certificates-error-code-151066)** 
- **[Site Recovery 的輸出連線能力 (錯誤碼 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)** 

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 資源配額問題 (錯誤碼 150097)
您的訂用帳戶應該啟用，才能在要做為災害復原區域的目標區域中建立 Azure VM。 此外，您的訂用帳戶應該已啟用足夠的配額，才能建立特定大小的 VM。 根據預設，Site Recovery 會取用相同大小的目標 VM 做為來源 VM。 如果沒有相符大小，系統會自動挑選最接近的大小。 如果沒有支援來源 VM 組態的相符大小，則會出現下列錯誤訊息：

**錯誤碼** | **可能的原因** | **建議**
--- | --- | ---
150097<br></br>**訊息**：虛擬機器 VmName 無法啟用複寫。 | - 可能未啟用您的訂用帳戶 ID 在目標區域位置中建立任何 VM。</br></br>- 可能未啟用您的訂用帳戶 ID 或沒有足夠的配額可在目標區域位置中建立特定 VM 大小。</br></br>- 對於目標區域位置中的訂用帳戶 ID，找不到符合來源 VM NIC 計數 (2) 的適當 VM 大小。| 對於訂用帳戶的目標位置之中的所需 VM 大小，請連絡 [Azure 計費支援](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)啟用 VM。 啟用後，請重試失敗的作業。

### <a name="fix-the-problem"></a>解决问题
您可以連絡 [Azure 計費支援](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)啟用訂用帳戶，在目標位置中建立所需大小的 VM。

如果目標位置有容量限制，請停用複寫，並對於訂用帳戶有足夠配額的不同位置啟用複寫，以建立所需大小的 VM。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根憑證 (錯誤碼 151066)

如果 VM 沒有所有最新的受信任根憑證，「啟用複寫」工作可能會失敗。 如果沒有憑證，對於從 VM 的 Site Recovery 服務呼叫進行的驗證和授權會失敗。 失敗的「啟用複寫」Site Recovery 工作的錯誤訊息會出現：

**錯誤碼** | 可能的原因 | **建議**
--- | --- | ---
151066<br></br>**訊息**：Site Recovery 設定失敗。 | 機器上不存在用於授權和驗證的受信任根憑證。 | - 對於執行 Windows 作業系統的 VM，請確定機器上存在受信任根憑證。 如需資訊，請參閱[設定受信任根目錄和不允許的憑證](https://technet.microsoft.com/library/dn265983.aspx)。<br></br>- 對於執行 Linux 作業系統的 VM，請依照 Linux 作業系統版本散發者發行的受信任根憑證適用的指引。

### <a name="fix-the-problem"></a>修正問題
**Windows**

在 VM 上安裝所有最新的 Windows 更新，使機器上存在所有的受信任根憑證。 如果您是在中斷連線的環境，請遵循您組織的標準 Windows 更新程序來取得憑證。 如果 VM 上不存在所需的憑證，對於 Site Recovery 進行的呼叫將而於安全性因素而失敗。

遵循您組織的一般 Windows 更新管理或憑證更新管理程序，取得所有最新的根憑證以及 VM 上更新的憑證撤銷清單。

若要確認問題已解決，請在 VM 中從瀏覽器移至 login.microsoftonline.com。

**Linux**

請遵循 Linux 散發者提供的指引，取得最新的受信任根憑證以及 VM 上最新的憑證撤銷清單。

因為 SuSE Linux 使用符號連結來維護憑證清單，因此請遵循下列步驟：

1.  以根使用者身分登入。

2.  執行這個命令來變更目錄。

      ``# cd /etc/ssl/certs``

1. 檢查是否有 Symantec 根 CA 憑證。

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. 如果找不到 Symantec 根 CA 憑證，請執行下列命令來下載檔案。 檢查是否有任何錯誤，並遵循適用於網路失敗的建議動作。

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. 檢查是否有 Baltimore 根 CA 憑證。

      ``# ls Baltimore_CyberTrust_Root.pem``

4. 如果找不到 Baltimore 根 CA 憑證，請下載憑證。  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. 檢查是否有 DigiCert_Global_Root_CA 憑證。

    ``# ls DigiCert_Global_Root_CA.pem``

6. 如果找不到 DigiCert_Global_Root_CA，請執行下列命令來下載憑證。

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. 執行 rehash 指令碼，來更新適用於最新下載憑證的憑證主體雜湊。

    ``# c_rehash``

8.  檢查是否已針對憑證建立主體雜湊作為符號連結。

    - 命令

      ``# ls -l | grep Baltimore``

    - 輸出

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - 命令

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - 輸出

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - 命令

      ``# ls -l | grep DigiCert_Global_Root``

    - 輸出

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  使用檔案名稱 b204d74a.0 建立 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 檔案的複本

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. 使用檔案名稱 653b494a.0 建立 Baltimore_CyberTrust_Root.pem 檔案的複本

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. 使用檔案名稱 3513523f.0 建立 DigiCert_Global_Root_CA.pem 檔案的複本

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. 檢查這些檔案是否存在。  

    - 命令

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - 輸出

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題 1：無法向 Site Recovery 註冊 Azure 虛擬機器 (151195) </br>
- **可能的原因** </br>
  - 因 DNS 解析失敗而無法建立與 Site Recovery 端點之間的連線。
  - 此問題經常會在您已將虛擬機器容錯移轉，但 DR 區域無法連線到 DNS 伺服器的情況下進行重新保護的期間發生。

- **解決方案**
   - 如果您是使用自訂 DNS，請確保災害復原區域可以連線到 DNS 伺服器。 若要查看您是否有自訂 DNS，請移至 VM> [災害復原網路] > [DNS 伺服器]。 嘗試從虛擬機器存取 DNS 伺服器。 如果無法存取，請試著對 DNS 伺服器進行容錯移轉，或在 DR 網路和 DNS 之間建立連線。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2：Site Recovery 設定失敗 (151196)
- **可能的原因** </br>
  - 無法建立與 Office 365 驗證與身分識別 IP4 端點之間的連線。

- **解決方案**
  - Azure Site Recovery 需要存取 Office 365 IP 範圍以進行驗證。
    如果您是使用 Azure 網路安全性群組 (NSG) 規則/防火牆 Proxy 來控制 VM 上的輸出網路連線能力，請確保您已允許與 O365 IP 範圍之間的通訊。 建立 [Azure Active Directory (AAD) 服務標籤](../virtual-network/security-overview.md#service-tags)型 NSG 規則，以允許存取對應至 AAD 的所有 IP 位址
      - 如果未來將新的位址新增至 Azure Active Directory (AAD)，您必須建立新的 NSG 規則。

> [!NOTE]
> 如果虛擬機器位於後方**標準**內部負載平衡器，然後它就不能存取 O365 Ip 也就是 根據預設 login.micorsoftonline.com。 請將它變更為**基本**內部負載平衡器類型，或建立出繫結的存取，如中所述[文章](https://aka.ms/lboutboundrulescli)。

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3：Site Recovery 設定失敗 (151197)
- **可能的原因** </br>
  - 無法在連線至 Azure Site Recovery 服務端點。

- **解決方案**
  - 根據區域的不同，Azure Site Recovery 需要存取不同的 [Site Recovery IP 範圍](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。 請確保虛擬機器可存取所需的 IP 範圍。


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>問題 4：當網路流量通過內部部署 Proxy 伺服器時，A2A 複寫失敗 (151072)
- **可能的原因** </br>
  - 自訂 Proxy 設定無效，且 ASR 行動服務代理程式未自動偵測到 IE 的 Proxy 設定


- **解決方案**
  1. 行動服務代理程式偵測到 IE 的 Proxy 設定 (在 Windows 上) 和 /etc/environment (在 Linux 上)。
  2. 如果您只想要為 ASR 行動服務設定 Proxy，則可在 ProxyInfo.conf 中提供 Proxy 詳細資料，該檔案位於：</br>
     - ``/usr/local/InMage/config/`` (在 ***Linux*** 上)
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` (在 ***Windows*** 上)
  3. ProxyInfo.conf 應該要有下列 INI 格式的 Proxy 設定。</br>
                [proxy]</br>
                Address=http://1.2.3.4</br>
                Port=567</br>
  4. ASR 行動服務代理程式僅支援***未驗證的 Proxy***。


### <a name="fix-the-problem"></a>修正問題
若要將[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或[所需的 IP 範圍](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)列入白名單中，請依照[網路指引文件](site-recovery-azure-to-azure-networking-guidance.md)中的步驟進行。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在機器中找不到磁碟 (錯誤碼 150039)

必須先將連接到 VM 的新磁碟初始化。

**錯誤碼** | **可能的原因** | 建议
--- | --- | ---
150039<br></br>**訊息**：邏輯單元編號 (LUN) 為 (LUNValue) 的 Azure 資料磁碟 (DiskName) (DiskURI) 未對應到從 LUN 值相同的 VM 內回報的對應磁碟。 | - 新的資料磁碟連接到 VM，但是未初始化。</br></br>- VM 內的資料磁碟不當回報 LUN 值，該磁碟已連接到 VM。| 請確定資料磁碟都已初始化，然後再次嘗試操作。</br></br>若為 Windows：[連接並初始化新的磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。</br></br>若為 Linux：[在 Linux 中初始化新的資料磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

### <a name="fix-the-problem"></a>修正問題
請確定資料磁碟都已初始化，然後再次嘗試操作：

- 若為 Windows：[連接並初始化新的磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。
- 對於 Linux：[在 Linux 中新增資料磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

若問題持續發生，請連絡支援服務。


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>看不見 Azure VM，無法在「啟用複寫」中選取

 **原因 1：資源群組和來源虛擬機器位於不同的位置** <br>
Azure Site Recovery 目前要求來源區域資源群組和虛擬機器應該位在相同的位置。 如果情況不是這樣，那麼在保護期間您就無法尋找虛擬機器。

**原因 2：資源群組不在選取的訂用帳戶中** <br>
如果資源群組不是指定的訂用帳戶的一部分，則可能無法在保護時尋找該資源組。 請確定資源群組屬於正在使用的訂用帳戶。

 **原因 3：過時的設定** <br>
如果您未看見想要啟用來進行複寫的 VM，可能是因為 Azure VM 上保留了過時的 Site Recovery 設定。 在下列情況中，Azure VM 可能保留過時的組態：

- 您使用 Site Recovery 啟用 Azure VM 的複寫，然後刪除 Site Recovery 保存庫，但是並未明確停用 VM 上的複寫。
- 您使用 Site Recovery 啟用 Azure VM 的複寫，然後刪除包含 Site Recovery 保存庫的資源群組，但是並未明確停用 VM 上的複寫。

### <a name="fix-the-problem"></a>修正問題

>[!NOTE] 
>
>請務必在使用下列指令碼之前，先更新 ""AzureRM.Resources"" 模組。 

您可以使用[移除過時 ASR 組態指令碼](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412)，並移除 Azure VM 上的過時 Site Recovery 組態。 在移除過時的設定之後，您應該就能看到該 VM。

## <a name="unable-to-select-virtual-machine-for-protection"></a>無法選取虛擬機器進行保護 
 **原因 1：虛擬機器在失敗或沒有回應的狀態下安裝了某些延伸模組** <br>
 移至 [虛擬機器] > [設定] > [延伸模組]，並檢查是否有任何延伸模組處於失敗狀態。 解除安裝失敗的延伸模組，並重試保護虛擬機器。<br>
 **原因 2：[VM 的佈建狀態無效](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>VM 的佈建狀態無效 (錯誤碼 150019)

若要在 VM 上啟用複寫，佈建狀態應該是「成功」。 您可以藉由遵循下列步驟來檢查 VM 狀態。

1.  在 Azure 入口網站中從 [所有服務] 選取 [資源總管]。
2.  展開 [訂用帳戶] 清單然後選取您的訂用帳戶。
3.  展開 [ResourceGroups] 清單然後選取 VM 的資源群組。
4.  展開 [資源] 清單然後選取您的虛擬機器
5.  勾選右側 [執行個體] 檢視中的 [provisioningState] 欄位。

### <a name="fix-the-problem"></a>修正問題

- 如果 [provisioningState] 是「失敗」，請連絡支援人員以取得疑難排解的詳細資訊。
- 如果 [provisioningState] 是「正在更新」，則可能有其他擴充功能正在部署。 檢查 VM 上是否有任何進行中的作業，等候它們完成然後重試失敗的 Site Recovery **啟用複寫**作業。

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>無法選取目標虛擬網路 - 網路選取索引標籤呈現灰色。

**原因 1：如果 VM 所連結的網路已對應至「目標網路」。**
- 如果來源 VM 屬於虛擬網路，且來自相同虛擬網路的另一個 VM 已對應至目標資源群組中的網路，則根據預設，系統會停用網路選取下拉式清單。

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**原因 2：如果您先前使用 Azure Site Recovery 保護 VM 並停用複寫。**
 - 停用 VM 的複寫不會刪除網路對應。 您必須從受保護 VM 所在的復原服務保存庫中將其刪除。 </br>
 瀏覽至 [復原服務保存庫] > [Site Recovery 基礎結構] > [網路對應]。 </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - 完成初始設定後，您可在 VM 受到保護後，對災害復原設定期間所設定的目標網路進行變更。 </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - 請注意，變更網路對應會影響所有使用該特定網路對應的受保護 VM。


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM+/磁碟區陰影複製服務錯誤 (錯誤碼 151025)

**錯誤碼** | **可能的原因** | **建議**
--- | --- | ---
151025<br></br>**訊息**：無法安裝站台復原擴充功能 | - 停用「COM+ 系統應用程式」服務。</br></br>- 停用「磁碟區陰影複製」服務。| 將「COM+ 系統應用程式」和「磁碟區陰影複製」服務設為自動或手動啟動模式。

### <a name="fix-the-problem"></a>修正問題

您可以開啟 [服務] 主控台，確定「COM+ 系統應用程式」和「磁碟區陰影複製」的 [啟動類型] 未設定為 [停用]。
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不支援的受控磁碟大小 (錯誤碼 150172)


**錯誤碼** | **可能的原因** | **建議**
--- | --- | ---
150172<br></br>**訊息**：無法對虛擬機器啟用保護作業，因為其 (DiskName) 的大小 (DiskSize) 小於支援的最小值 1024 MB。 | - 磁碟小於支援的大小 1024 MB| 請確定磁碟大小在支援的大小範圍內，然後重試作業。 

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>啟用保護失敗，原因是 GRUB 組態中所提的裝置名稱，而不是 UUID (錯誤碼 151126)

**可能的原因：** </br>
GRUB 組態檔 ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") 可能包含 **root** 和 **resume** 參數的值作為實際裝置名稱，而不是 UUID。 Site Recovery 會強制執行 UUID 方法，因為裝置名稱可能會經由 VM 重新開機而變更，而且 VM 可能不會提出容錯移轉時的相同名稱而造成問題。 例如︰ </br>


- 下面這一行來自 GRUB 檔案 **/boot/grub2/grub.cfg**。 <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- 下面這一行來自 GRUB 檔案 **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

如果您發現上述的粗體字串，則 GRUB 具有 "root" 和 "resume" 參數的實際裝置名稱，而不是 UUID。

**修正方式：**<br>
裝置名稱應該取代為對應的 UUID。<br>


1. 執行命令 "blkid <device name>" 來尋找裝置的 UUID。 例如︰<br>
   ```
   blkid /dev/sda1 
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br> 
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device mentioned in the GRUB configuration doesn't exist(error code 151124)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples: 

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg". 
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg". 
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  site recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:** 
Delete the mentioned replica disk in the error message and restart the failed protection job again. 

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
