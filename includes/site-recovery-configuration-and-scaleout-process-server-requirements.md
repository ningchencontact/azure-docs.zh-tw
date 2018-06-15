| **元件** | **需求** |
| --- |---|
| CPU 核心| 8 |
| RAM | 16 GB|
| 磁碟數量 | 3，包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機 |
| - 磁碟可用空間 (處理序伺服器快取) | 600 GB
| 磁碟可用空間 (保留磁碟) | 600 GB|
| 作業系統  | Windows Server 2012 R2 <br> Windows Server 2016 |
| 作業系統地區設定 | 英文 (en-us)|
| VMware vSphere PowerCLI 版本 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V |
| 群組原則| 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - 沒有預先存在的預設網站 <br> - 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) \(英文\) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定  <br> - 沒有預先存在的網站/應用程式接聽連接埠 443<br>|
| NIC 類型 | VMXNET3 (部署為 VMware VM 時) |
| IP 位址類型 | 靜態 |
| 網際網路存取 | 伺服器需要存取這些 URL： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (不需要相應放大處理序伺服器) <br> - time.nist.gov <br> - time.windows.com <br> \*.visualstudio.com |
| 連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸)|
