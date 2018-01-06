
 
最近公開[CPU 弱點的新類別](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)稱為推測執行側邊通道攻擊會導致從客戶尋求更清楚的問題。 

 
## <a name="azure-infrastructure"></a>Azure 基礎結構

略過 hypervisor 界限，並允許洩漏的兩個共同代管的虛擬機器之間的記憶體可用的弱點可能會洩漏中所述的問題。 中稍早所報告[部落格文章](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)，Azure 已套用保護客戶對這項弱點的防護措施。  Microsoft 一直建議客戶套用您的 VM 映像，包括其作業系統廠商安裝的所有安全性更新的安全性最佳作法。

## <a name="paas-services-on-azure"></a>在 Azure 上的 PaaS 服務
Azure PaaS 供應項目具有預設部署防護功能。 客戶不需要任何動作。 Azure 雲端服務例外狀況，請參閱下方內容。  


## <a name="azure-cloud-services"></a>Azure 雲端服務

[Azure 雲端服務](https://azure.microsoft.com/services/cloud-services/)接收自動更新啟用自動包含這些弱點的防護的客體 os 版本。 

下列客體作業系統版次中包含更新的保護來防止推測執行側邊通道弱點：

* WA-客體-OS-5.15_201801-01
* WA-客體-OS-4.50_201801-01


少數的客戶裝載未受信任的程式碼中使用 Azure 雲端服務也應該啟用[核心虛擬位址，以遮蔽](#enabling-kernel-virtual-address-shadowing-on-windows-server)除了更新客體 OS。 這會提供額外的防護措施推測執行側邊通道弱點。 這可以透過啟動工作完成。 下面會提供有關哪些客戶和使用方式案例需要這項功能，以及如何啟用它的詳細資訊。


## <a name="azure-virtual-machines-windows--linux"></a>Azure 虛擬機器 （Windows 和 Linux）

Microsoft 一直建議客戶安裝的所有安全性更新。 

年 1 月 2018年安全性彙總包含這些弱點等等的修正。 請確認您安裝作業系統更新之前，先執行支援的防毒應用程式。 相容性資訊，請連絡防毒軟體廠商。 

針對推測執行弱點，Linux 核心就必須執行，並可從您散發的提供者時可用來取得更新。 

少數的客戶裝載未受信任的程式碼中使用 Azure 虛擬機器 (Windows) 也應該啟用[核心虛擬位址，以遮蔽](#enabling-kernel-virtual-address-shadowing-on-windows-server)以提供額外的防護措施推測執行側邊通道弱點。  下面會提供有關哪些客戶和使用方式案例會需要這項功能，以及如何加以啟用的詳細資訊。


## <a name="enabling-kernel-virtual-address-shadowing-on-windows-server"></a>啟用 Windows Server 上，以遮蔽核心虛擬位址

執行不受信任的程式碼使用 Windows Server 的客戶應該啟用稱為核心虛擬位址，以遮蔽提供保護系統不受信任的程式碼以低的使用者權限的執行所在的功能。

此額外的保護可能會影響效能，並依預設已關閉。 核心虛擬位址，以遮蔽可防止程序來處理和核心同處理序的資訊洩漏。

您的 Vm、 雲端服務或在內部部署伺服器有風險如果它們分成下列類別之一：

* Azure 巢狀虛擬化 HYPER-V 主機
* 遠端桌面服務主機 (RDSH)
* 虛擬機器或雲端服務會執行不受信任的程式碼，例如容器或不受信任的資料庫、 不受信任的 web 內容或工作負載的擴充功能可執行程式碼從外部來源所提供。

是必要核心虛擬位址，以遮蔽的案例的範例： 

|     |
|-----|
|Azure 虛擬機器執行的服務不受信任的使用者可以提交 JavaScript 程式碼執行所在之有限權限。 在相同的 VM 上有高特殊權限的程序包含不應該存取這些使用者的密碼資料。 在此情況下，核心虛擬位址的遮蔽功能是為了提供保護，防止外洩，兩者之間。|
|     | 

在啟用核心虛擬位址，以遮蔽的特定指示都是透過[Windows 伺服器 KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)。


> [!NOTE]
> 在發行集時，核心虛擬位址的遮蔽功能才可以使用 Windows Server 2016、 Windows Server 2012 R2 和 Windows Server 2008 R2 中。  
>
>

如果您執行 Linux 伺服器，請洽詢您的作業系統更新和指示的廠商。

## <a name="branch-target-injection-mitigation-support-microcode"></a>分支目標資料隱碼攻擊的防護支援 （微碼）

使用工具，可以偵測微碼基礎防護功能的存在客戶回報 Azure 是未修補。 這是不正確。 在此發行集時，分支目標資料隱碼攻擊的風險降低支援不會公開從 Azure Hypervisor 到 Azure 虛擬機器或 Azure 雲端服務。 這表示虛擬機器將不知道微碼存在，而且不能使用它的增強型的指令集。 這不表示 Azure 很容易受到跨 VM 推測執行側邊通道攻擊。
 
因為我們產業合作夥伴合作，以處理更多更新可能會變成可用。

## <a name="next-steps"></a>後續步驟

若要進一步了解，請參閱[CPU 的弱點可能會保護 Azure 客戶](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)。